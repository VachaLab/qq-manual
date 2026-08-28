# Why does qq exist?

qq was written with three goals in mind:

1. To provide job management utilities that are simpler to use, easier to understand, or simply do more than the utilities PBS and Slurm come with.
2. To take the work out of using scratch storage on Metacentrum Grid.
3. To offer a single command-line interface and Python API that works the same way on both PBS Pro and Slurm.

## Goal 1: Utilities

The output of PBS Pro and Slurm commands is verbose and can be hard to read. qq reformats it: [`qq jobs`](commands/qq_jobs.md), [`qq nodes`](commands/qq_nodes.md) and [`qq queues`](commands/qq_queues.md) list jobs, nodes and queues in a nice, human-readable format.

qq also provides commands that PBS and Slurm do not have. For instance, [`qq info`](commands/qq_info.md) provides detailed, persistent information about a job, [`qq cd`](commands/qq_cd.md) and [`qq go`](commands/qq_go.md) navigate to a job's input and scratch directory, respectively, [`qq wipe`](commands/qq_wipe.md) cleans failed job's working directory, and [`qq respawn`](commands/qq_respawn.md) can submit a failed job again. qq also supports [loop jobs](job_types/loop_job.md): a loop job is a batch job that submits its continuation for a set number of cycles.

## Goal 2: Simplified scratch management

Scratch storage on Metacentrum Grid is managed manually. A job script has to check that the scratch directory exists, copy the input files into it, register a trap to clean it up, copy the results back afterwards, and handle failures at each step.

qq [does this for you](job_types/standard_job.md#2-preparing-the-working-directory). It sets up the scratch directory, transfers input files from the shared Metacentrum storage, and transfers the output back when the job ends. On success the scratch directory is removed; on failure it is left in place for inspection.

### Batch job script example

Here is an example of how much simpler qq makes writing batch job scripts compared to a standard PBS job script on Metacentrum Grid.

#### Metacentrum Grid PBS job script

```bash
#!/bin/bash

#PBS -N gmx_md
#PBS -l select=1:ncpus=8:ompthreads=8:mem=8gb:scratch_local=8gb
#PBS -q default
#PBS -l walltime=24:00:00

# directory where the input files are located
DATADIR="${PBS_O_WORKDIR}"

# test whether a scratch directory exists
test -n "${SCRATCHDIR}" || { echo >&2 "Variable SCRATCHDIR is not set!"; exit 1; }

# the scratch directory should be cleaned automatically
trap "clean_scratch" TERM EXIT

# copy input files to the scratch directory
cp -r "${DATADIR}"/{md.mdp,eq.gro,eq.cpt,index.ndx,system.top,ff} "${SCRATCHDIR}/" \
    || { echo >&2 "Copying input files failed!"; exit 2; }

# go to the scratch directory
cd "${SCRATCHDIR}" || { echo >&2 "Cannot enter ${SCRATCHDIR}!"; exit 3; }

# activate the Gromacs module
module add gromacs/2024.3-cpu

# set the number of OpenMP threads to the requested number of CPUs
export OMP_NUM_THREADS="${PBS_NUM_PPN}"

# prepare a TPR file
gmx_mpi grompp -f md.mdp -c eq.gro -t eq.cpt -n index.ndx -p system.top -o md.tpr

# run the simulation using 8 OpenMP threads
gmx_mpi mdrun -deffnm md -ntomp "${OMP_NUM_THREADS}"
MDRUN_RC=$?

# handle mdrun exit code
if [ ${MDRUN_RC} -ne 0 ]; then
    echo >&2 "mdrun exited with ${MDRUN_RC} - keeping ${SCRATCHDIR} on $(hostname -f)"
    export CLEAN_SCRATCH=false
    exit $MDRUN_RC
fi

# copy output files back to the shared Metacentrum storage
cp -r "${SCRATCHDIR}"/* "${DATADIR}/" || export CLEAN_SCRATCH=false
```

Submit as `qsub <script-file>`.

#### qq job script

```bash
#!/usr/bin/env -S qq run

# qq queue default
# qq ncpus 8
# qq walltime 1d

# activate the Gromacs module
module add gromacs/2024.3-cpu

# prepare a TPR file
gmx_mpi grompp -f md.mdp -c eq.gro -t eq.cpt -n index.ndx -p system.top -o md.tpr

# run the simulation using 8 OpenMP threads
gmx_mpi mdrun -deffnm md -ntomp "${QQ_NCPUS}"
```

Submit as `qq submit <script-file>`.

## Goal 3: Unified command-line interface and Python API

PBS Pro and Slurm use different commands for the same operations. qq provides one command-line interface for both, so the same commands work on Metacentrum Grid and on IT4Innovations' Karolina. Everything the command line can do is also available through a [Python API](https://qq.readthedocs.io/en/stable/qq_lib.html), for use in scripts.

---

## Non-goals

There are some things qq does not try to be:

- qq is **not** a batch system, it is a wrapper around one. PBS Pro or Slurm still does the actual work, and qq will never replace either of them. It will also never expose everything they can do — only the subset that the users most commonly need.
- qq is **not** a general-purpose tool for any HPC system. Only specific clusters are supported.

> [!NOTE]
> qq is developed primarily for the internal use of the [Robert Vácha Lab](https://vacha.ceitec.cz/), and our group's needs come first. If qq is missing something you need, or you think it should work differently, [tell us](https://github.com/VachaLab/qq/issues). We will listen, but we cannot promise to change it. Some things may also not work on your system simply because it differs from ours. Let us know if that happens and we will try to help, though we cannot guarantee that qq will work for you in every case.

# Running a job

This section demonstrates how to run a basic qq job by performing a simple Gromacs simulation on the robox cluster. This assumes that you have already successfully installed qq on the cluster.

## 1. Preparing an input directory

Start by creating a directory for the job on a shared storage (*you can also submit from a local storage on your computer but that is not recommended*). This directory should contain all necessary simulation input files — in this example, an `mdp`, `gro`, `cpt`, `ndx`,`top`, and `itp` files.

## 2. Preparing a run script

Next, prepare a run script that creates a `tpr` file from the input files using `gmx grompp`, and then runs the simulation with `gmx mdrun`. We’ll configure the simulation to use 8 OpenMP threads.

**Note that all qq run scripts must start with the correct shebang line:**

```bash
#!/usr/bin/env -S qq run
```

A complete example of a run script:

```bash
#!/usr/bin/env -S qq run

# activate the Gromacs module
metamodule add gromacs/2024.3-cuda

# prepare a TPR file
gmx_mpi grompp -f md.mdp -c eq.gro -t eq.cpt -n index.ndx -p system.top -o md.tpr

# run the simulation using 8 OpenMP threads
gmx_mpi mdrun -deffnm md -ntomp 8 -v
```

Save this file as `run_job.sh` and make it executable:
```bash
chmod u+x run_job.sh
```

## 3. Submitting the job

Submit the job using [`qq submit`](qq_submit.md):

```bash
qq submit run_job.sh -q cpu --ncpus 8 --walltime 1d
```

This submits `run_job.sh` to the `cpu` queue, requesting 8 CPU cores and a walltime of one day. All other parameters are determined by the queue or qq’s default settings.

The batch system then schedules the job for execution. Once a suitable compute node is available, the job runs through [`qq run`](qq_run.md), a wrapper around bash that prepares the [working directory](work_dir.md), copies files, executes the script, and performs cleanup. You can read more about how exactly this works in [this section](standard_job.md) of the manual.

## 4. Inspecting the job

After submission, you can inspect the job using [`qq info`](qq_info.md), access its working directory on the compute node with [`qq go`](qq_go.md), or terminate it using [`qq kill`](qq_kill.md). For an overview of all qq commands, see [this section](commands.md) of the manual.

## 5. Getting the results

Once the job finishes, the resulting Gromacs output files will be transferred from the working directory back to the original input directory. You can verify that everything completed successfully using [`qq info`](qq_info.md).

If your job failed (crashed) or was killed, the output files will **not** be transferred to ensure your input directory remains in a consistent state. In these cases, the working directory on the compute node is preserved, allowing you to inspect the job files directly there using [`qq go`](qq_go.md), or to explicitly copy them back to the input directory using [`qq sync`](qq_sync.md).

***

## Run scripts

For more complex setups — particularly for running Gromacs simulations in loops — [qq provides several ready-to-use run scripts](run_scripts.md). These scripts are fully compatible with all qq-supported clusters, including both Metacentrum-family clusters and Karolina.
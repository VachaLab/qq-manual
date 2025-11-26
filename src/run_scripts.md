# Run scripts

The [qq GitHub repository](https://github.com/Ladme/qq/tree/main/scripts/run_scripts) provides several ready-to-use scripts for running [Gromacs](https://www.gromacs.org/) simulations in loops — similar to Infinity’s `precycle` scripts.

These scripts are compatible with all qq-supported clusters, including Metacentrum-family clusters, Karolina, and LUMI. **Do not forget to load Gromacs from the module appropriate for the given cluster.**

> **For LUMI users:** If you are using full nodes on the LUMI's GPU queues, the run scripts may require some modifications to get solid performance (see [here](https://docs.csc.fi/apps/gromacs/#full-gpu-node-batch-script)).

---

## [qq_loop_md](https://github.com/Ladme/qq/blob/main/scripts/run_scripts/qq_loop_md)

A job script for running **single-directory Gromacs simulations** in loops.

Start by preparing a directory containing all necessary input files, and place `qq_loop_md` inside it.

In your `.mdp` file, specify the number of simulation steps to perform **in each cycle**. In the body of the script, set the total number of cycles to run (using the `qq loop-end` directive), define input filenames, specify the Gromacs module to load, and optionally adjust the number of MPI ranks and OpenMP threads to use. By default, qq assigns one MPI rank per CPU core. If any GPUs are requested, one MPI rank per GPU is used and the remaining CPU cores are distributed among the MPI ranks as OpenMP threads.

Once ready, submit the job with `qq submit`. The first cycle will be submitted and executed, and before it finishes, qq will automatically submit the next cycle. Read more about qq loop jobs [here](loop_job.md).

The total simulation length after all cycles finish equals: (steps per cycle in the `.mdp` file) × (number of cycles).

---

## [qq_flex_md](https://github.com/Ladme/qq/blob/main/scripts/run_scripts/qq_flex_md)

A job script for running **single-directory Gromacs simulations** in flexible-length loops.

This script works similarly to `qq_loop_md`, but here the `.mdp` file specifies the **total number of simulation steps** to perform. Each cycle runs until it either exhausts its walltime or reaches the specified total number of steps. When a cycle ends, the script checks whether the target step count has been reached; if not, it automatically submits the next cycle. As a result, each cycle may have a different duration.

The total simulation length after all cycles finish corresponds to the number of steps specified in the `.mdp` file.

---

## [qq_loop_re](https://github.com/Ladme/qq/blob/main/scripts/run_scripts/qq_loop_re)

A job script for running **multi-directory Gromacs simulations** in loops. It functions like `qq_loop_md`, but instead of a single simulation, it manages multiple simulations across several subdirectories.

Place `qq_loop_re` in the **parent directory** containing your subdirectories. In the script body, specify the naming pattern for the subdirectories (e.g., `win` for `win01`, `win02`, ..., `win42`, etc.).

This script is typically used for **replica exchange simulations** (hence the `re` in its name). You can thus also set the exchange attempt frequency and choose whether to perform Hamiltonian replica exchange.

Note that by default, `qq_loop_re` and `qq_flex_re` scripts use a single MPI rank per GPU (if requested) or per CPU core.

The total simulation length after all cycles finish equals: (steps per cycle in the `.mdp` file) × (number of cycles).

---

## [qq_flex_re](https://github.com/Ladme/qq/blob/main/scripts/run_scripts/qq_flex_re)

A job script for running **multi-directory Gromacs simulations** in flexible-length loops — essentially a hybrid of `qq_loop_re` and `qq_flex_md`.

Each cycle runs until it reaches the specified total number of steps or the walltime limit, automatically submitting the next cycle if needed.

The total simulation length after all cycles finish equals the number of steps specified in the `.mdp` file.

---

## Prolonging the simulations

After your simulations finish, you may find that you want them to continue for a bit longer.

### qq_loop_md / qq_loop_re

Prolonging simulations run with `qq_loop_*` scripts is straightforward. Increase the value in the `# qq loop-end ...` directive to extend the total number of cycles, then submit the loop script again using [`qq submit`](qq_submit.md). You do not need to remove the runtime files in the directory. The loop job will resume from the next cycle.

### qq_flex_md / qq_flex_re

Prolonging simulations run with `qq_flex_*` scripts is a bit more involved: you must extend the Gromacs `tpr` file (or **files**, in the case of `qq_flex_re`) to include more simulation time.

You can do this with:

```bash
gmx_mpi convert-tpr -s storage/md<NEXT_CYCLE_NUMBER>.tpr -until <TOTAL_SIMULATION_RUN> -o storage/md<NEXT_CYCLE_NUMBER>.tpr
```

*`<NEXT_CYCLE_NUMBER>` is the number of the **next** cycle of the loop job. `<TOTAL_SIMULATION_RUN>` is the new **total** simulation time in picoseconds. See the [documentation of gmx convert-tpr](https://manual.gromacs.org/current/onlinehelp/gmx-convert-tpr.html) for more details.*

If you are using `qq_flex_re`, you must update `tpr` files for all clients created for the next cycle in the `storage` directory. Their names follow this format:

```
md<NEXT_CYCLE_NUMBER>-<DIRECTORY_IDENTIFIER>.tpr
```

Once the `tpr` files are updated, simply submit the flex script again using [`qq submit`](qq_submit.md). You do not need to remove the runtime files in the directory.

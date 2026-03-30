# LUMI supercomputer

- LUMI has four different storages:
    - [User home](https://docs.lumi-supercomputer.eu/storage/#__tabbed_1_1) (`/users/<username>`) — small capacity
    - [Project space](https://docs.lumi-supercomputer.eu/storage/#__tabbed_1_2) (`/project/<project_id>`) — small capacity, slow
    - [Project scratch](https://docs.lumi-supercomputer.eu/storage/#__tabbed_1_3) (`/scratch/<project_id>`) — large capacity, fast
    - [Project flash](https://docs.lumi-supercomputer.eu/storage/#__tabbed_1_4) (`/flash/<project_id>`) — medium capacity, super fast

- All storages are shared across all nodes of the supercomputer.

- All storages are persistent for the duration of the project, i.e., data are not deleted from any of them until the project completes. However, you are billed for using the storages, so try to keep the amount of stored data low.

- **It is recommended to store simulation data on the scratch space and submit jobs from there. When doing so, submit with the `--workdir input_dir` option so that your data are not needlessly copied around.**

- If you want to know more about configuring the storage qq uses for your job, read [this section of the manual](../resources/work_dir_lumi.md).

- Submit jobs with the `--account` option providing your project ID. You can find your project ID by running `lumi-allocations` (project ID should look like this: `project_123456`).

- When submitting to the `standard-g` (CPU+GPU) or `standard` (CPU-only) queues, you need to allocate full nodes. On each node, you can request 56 (`standard-g`) or 128 (`standard`) CPU cores.

- For most Gromacs simulations, it is recommended to simulate multiple systems as part of a single node-wide job. You can use the [`qq_loop_re`](../run_scripts.md#qq_loop_re) or the [`qq_flex_re`](../run_scripts.md#qq_flex_re) run scripts for that.

- When submitting to the `small-g` (CPU+GPU) or `small` (CPU-only) queues, you can allocate a smaller amount of resources.

- The **strongly recommended** ratio of GPUs to CPU cores on all GPU queues is 1:**7** (see [here](https://docs.lumi-supercomputer.eu/runjobs/scheduled-jobs/lumig-job/) for more details).

- Each LUMI CPU core can run two threads. This means that when you request N CPU cores (e.g., 7), `qq jobs` will report your job as using 2N cores (e.g., 14) once it starts running. This is expected behavior.

- When running Gromacs, you can choose between using N or 2N OpenMP threads per node. Depending on your setup, one may perform better than the other. By default, [qq run scripts](../run_scripts.md) for Gromacs use N OpenMP threads. To use 2N threads instead, replace the following lines in the scripts
    ```bash
        export OMP_NUM_THREADS="${NTOMP}"
        (...)
        ${PLUMED} -ntomp ${NTOMP} ${APPEND} -nb ${NB} -pin on -maxh ${MAX_TIME}
    ```

    with
    
    ```bash
        export OMP_NUM_THREADS="$((NTOMP * 2))"
        (...)
        ${PLUMED} -ntomp $((NTOMP * 2)) ${APPEND} -nb ${NB} -pin on -maxh ${MAX_TIME}
    ```

- LUMI's compute nodes have fast interconnections, making it easy to efficiently run jobs across multiple nodes.

> Click here for detailed [external documentation](https://docs.lumi-supercomputer.eu/) of the LUMI supercomputer.
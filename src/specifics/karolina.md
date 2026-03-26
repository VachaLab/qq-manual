# Karolina supercomputer

- Karolina has three different storages: 
    - [Home storage](https://docs.it4i.cz/en/docs/clusters/karolina/storage#home-file-system): small capacity
    - [Project storage](https://docs.it4i.cz/en/docs/storage/project/project-storage): large capacity, slow, persistent
    - [Scratch storage](https://docs.it4i.cz/en/docs/clusters/karolina/storage#scratch-file-system): large capacity, fast, regularly cleared

- All storages are shared across all nodes of the supercomputer.

- It is recommended to store simulation data in the Project storage and submit jobs from there. Data on the Project storage are not deleted until the project finishes, and this storage has a large capacity (unlike Home storage).

- Prefer to **not** submit jobs from the Scratch storage, as its content is regularly deleted and you can lose your data.

- With default options, when submitting from the Project storage, qq automatically creates a directory on Scratch storage, executes your job there, and transfers the data back. qq takes care of these copying operations.

- If you want to know more about configuring the storage qq uses for your job, read [this section of the manual](../resources/work_dir_karolina.md).

- Submit jobs with the `--account` option providing your project ID. You can find your project ID by running `it4ifree` (left-most column, in the format `OPEN-XX-YY`).

- When submitting CPU-only jobs (queues starting with `qcpu`), you always need to allocate a full compute node. Each CPU node has 128 CPU cores. If you do not specify the number of CPUs, qq will use the correct value automatically.

- For most Gromacs simulations, it is recommended to simulate multiple systems as part of a single node-wide job. You can use the [`qq_loop_re`](../run_scripts.md#qq_loop_re) or the [`qq_flex_re`](../run_scripts.md#qq_flex_re) run scripts for that.

- When submitting CPU+GPU jobs (queues starting with `qgpu`), you can allocate as little as 1/8 of a compute node, which corresponds to 1 GPU and 16 CPU cores.

- Karolina's compute nodes have fast interconnections, making it easy to efficiently run jobs across multiple nodes.

> Click here for detailed [external documentation](https://docs.it4i.cz/en/docs/clusters/karolina/introduction) of the Karolina supercomputer.
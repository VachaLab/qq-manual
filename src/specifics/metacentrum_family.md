# Robox, Sokar, and Metacentrum clusters

- Use shared storages (e.g., `brno14-ceitec`, `brno12-cerit`) for storing your simulation data and submitting jobs.

- With default options, qq automatically allocates storage on the compute node(s), executes your job there, and transfers the data back. qq takes care of these copying operations.

- If you want to know more about configuring the storage qq uses for your job, read [this section of the manual](../resources/work_dir_metacentrum.md).

- When writing scripts to be executed, you can assume that all files in the script's parent directory will be accessible using relative paths. You can also use absolute paths to access files on shared storages. However, you cannot easily access the local storage on your desktop.

- From your Robox desktop, you can submit jobs to all Metacentrum-family clusters (see [Inter-cluster job management](../servers.md)).

- Metacentrum-family clusters are very heterogeneous — you can use pretty much any number of CPUs and GPUs you need, as long as they fit on a single node. Running multi-node jobs can be complicated, as most clusters do not have fast interconnections between individual compute nodes.

- When submitting jobs to Metacentrum, submit with `--props ^cl_samson` to avoid using the `samson` node, which does not support qq.

- On Metacentrum, some nodes or node groups may be slow or unstable. You can filter them out by submitting with `--props ^cl_<cluster_name>` to exclude a cluster, or with `--props vnode=^<node_name>` to exclude a specific node.

> Click here for detailed [external documentation](https://docs.metacentrum.cz/en/docs/welcome) of the Metacentrum family clusters.
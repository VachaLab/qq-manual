# Number of CPU cores

Each compute node has a fixed number of CPU cores. You can typically request a subset of them for your job. You can specify the number of CPU cores either per node or in total:

- `--ncpus-per-node` — specifies the number of CPU cores per requested compute node.
- `--ncpus` — specifies the total number of CPU cores for the entire job. Overrides `--ncpus-per-node`.

For example, if you request 2 nodes and 16 CPU cores per node, your job will have 32 CPU cores in total. You can express this either as `--nnodes 2 --ncpus-per-node 16` or `--nnodes 2 --ncpus 32`.
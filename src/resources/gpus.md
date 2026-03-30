# Number of GPUs

Some compute nodes are equipped with GPUs, which can dramatically speed up certain types of computations — particularly in machine learning, molecular dynamics, and other highly parallelizable workloads. You can specify the number of GPUs either per node or in total:

- `--ngpus-per-node` — specifies the number of GPUs per requested compute node.
- `--ngpus` — specifies the total number of GPUs for the entire job. Overrides `--ngpus-per-node`.

# Number of nodes

Use `--nnodes` to specify the number of compute nodes to allocate for the job. 

Most jobs only need a single node, and this is typically the default. You only need to request multiple nodes if your job uses a parallelization framework that supports multi-node execution, such as MPI.
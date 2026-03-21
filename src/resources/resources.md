# Specifying resources

Each qq job requires some resources to run. These resources need to be requested at job submission time — the batch system uses this information to find suitable compute nodes and to ensure that jobs do not interfere with each other. Requesting too little may cause your job to fail or get killed; requesting too much may result in longer queue waiting times.

You can specify resources on the command line when running [`qq submit`](../commands/qq_submit.md), or inside the submitted script itself using [qq directives](../commands/qq_submit.md#specifying-options-in-the-script). If a resource is not specified, its value falls back to the queue default, then the server default, and finally the qq-level default for the given environment — in that order of priority.

> In this section of the manual, every time you see an option, something like `--nnodes`, `--ncpus`, or `--walltime`, these options relate to the [`qq submit`](../commands/qq_submit.md) command.
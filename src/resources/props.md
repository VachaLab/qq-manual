# Node properties

Some clusters have compute nodes with special hardware or software configurations, identified by *node properties*. Use `--props` to specify which properties are required or prohibited for your job. The value is a colon-, comma-, or space-separated list of property expressions.

A property can be a simple boolean flag (a node either has it or it doesn't), or it can carry a specific value, in which case it is expressed as `property=value`. To prohibit a property or a property value, prefix it with `^`. For example:

- `--props cl_two` — the job will only run on nodes that have the `cl_two` property.
- `--props ^cl_two` — the job will only run on nodes that do not have the `cl_two` property.
- `--props cl_two,gpu_v100` — the job will only run on nodes that have both the `cl_two` and `gpu_v100` properties.
- `--props gpu_model=A100` — the job will only run on nodes equipped with an A100 GPU.
- `--props gpu_model=^A100` — the job will **not** run on nodes equipped with an A100 GPU.

You can use [`qq nodes`](../commands/qq_nodes.md) to browse the available nodes and their properties.

> Note that prohibiting property values is only supported for the PBS batch system (on Robox, Sokar, Metacentrum).

### vnode property

On Robox, Sokar, and Metacentrum, each compute node has a special `vnode` property identifying that specific node. The value of the `vnode` attribute corresponds to the name of the node. You can use this attribute to force your job to run on a particular node (`--props vnode=zeroc1`) or to prevent it from running there (`--props vnode=^zeroc1`).
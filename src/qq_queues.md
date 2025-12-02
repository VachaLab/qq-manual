# qq queues

The `qq queues` command displays the queues available on the current batch server. It is qq's equivalent of Infinity's `pqueues`.

***

> **Quick comparison with pqueues**
> - `qq queues` is generally more accurate at identifying available and unavailable queues than `pqueues`.
> - The only other notable difference is the output format.

***

### Description

Displays information about the queues available on the current batch server. By default, only queues that are available to you are shown.

```bash
qq queues [OPTIONS]
```

#### Options

`-a`, `--all` — Display all queues, including those that are not available to you.

`--yaml` — Output queue metadata in YAML format.

### Examples

```bash
qq queues
```

Displays a summary of all batch system queues to which you can submit jobs. The output might look like this:

![Example of qq queues output](img/qq_queues.png)

```bash
qq queues --all
```

Displays a summary of all queues in the batch system, including those you cannot submit to. The output might look like this *(truncated)*:

![Example of qq queues output](img/qq_queues_all.png)

*Output truncated.*

```bash
qq queues --yaml
```

Prints a summary of all available queues in YAML format. This output contains the full metadata provided by the batch system.

### Description of the output

![Example and a description of qq queues output](img/qq_queues_description.png)

- The output may also contain the column `Comment` providing the comment associated with the queue (typically additional information about the queue).
- `Max Nodes` column is hidden if no queue defines a maximal allowed number of requested nodes per job.
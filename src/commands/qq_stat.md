# qq stat

The `qq stat` command displays information about jobs from all users. It is qq's equivalent of Infinity's `pqstat`.

***

> **Quick comparison with pqstat**
> - The same differences that apply between [`qq jobs`](qq_jobs.md) and `pjobs` also apply here.

***

### Description

Displays a summary of jobs from all users. By default, only uncompleted jobs are shown.

```bash
qq stat [OPTIONS]
```

#### Options

`-e`, `--extra` — Include extra information about the jobs.

`-a`, `--all` — Include both uncompleted and completed jobs in the summary.

`-s` `TEXT`, `--server` `TEXT` — Show jobs for a specific batch server. If not specified, jobs on the default batch server are shown.

`--yaml` — Output job metadata in YAML format.

### Examples

```bash
qq stat
```

Displays a summary of all uncompleted (queued, running, or exiting) jobs associated with the default batch server. The display looks similar to the display of [`qq jobs`](qq_jobs.md#description-of-the-output).

```bash
qq stat -e
```

Includes extra information about the jobs in the output: the input machine (if available), the input directory, and the job comment (if available).

```bash
qq stat --all
```

Displays a summary of all jobs associated with the default batch server, both uncompleted and completed. Note that the batch system eventually removes records of completed jobs, so they may disappear from the output over time.

```bash
qq stat --server sokar
```

Displays a summary of all uncompleted jobs associated with the `sokar` batch server that are available to you. `sokar` is a known shortcut for the full batch server name `sokar-pbs.ncbr.muni.cz`. You can use either of them. For more information about accessing information from other clusters, read [this section of the manual](../servers.md#qq-jobs-qq-stat-qq-queues-qq-nodes).

```bash
qq stat --yaml
```

Prints a summary of all unfinished jobs in YAML format. This output contains all metadata provided by the batch system.

### Notes

- This command lists all types of jobs, including those submitted using `qq submit` and jobs created through other tools.  
- The run times and job states may not exactly match the output of `qq info`, since `qq stat` relies solely on batch system data and does not use qq info files.
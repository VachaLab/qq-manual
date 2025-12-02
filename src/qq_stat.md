# qq stat

The `qq stat` command displays information about jobs from all users. It is qq's equivalent of Infinity's `pqstat`.

***

> **Quick comparison with pqstat**
> - The same differences that apply between [`qq jobs`](qq_jobs.md) and `pjobs` also apply here.

***

### Description

Displays a summary of jobs from all users. By default, only unfinished jobs are shown.

```bash
qq stat [OPTIONS]
```

#### Options

`-e`, `--extra` — Include extra information about the jobs.

`-a`, `--all` — Include both unfinished and finished jobs in the summary.

`--yaml` — Output job metadata in YAML format.

### Examples

```bash
qq stat
```

Displays a summary of all unfinished (queued, running, or exiting) jobs associated with the current batch server. The display looks similar to the display of [`qq jobs`](qq_jobs.md#description-of-the-output).

```bash
qq stat -e
```

Includes extra information about the jobs in the output: the input machine (if available), the input directory, and the job comment (if available).

```bash
qq stat --all
```

Displays a summary of all jobs in the batch system, both unfinished and finished. Note that the batch system eventually removes information about finished jobs, so they may disappear from the output over time.

```bash
qq stat --yaml
```

Prints a summary of all unfinished jobs in YAML format. This output contains all metadata provided by the batch system.

### Notes

- This command lists all types of jobs, including those submitted using `qq submit` and jobs created through other tools.  
- The run times and job states may not exactly match the output of `qq info`, since `qq stat` relies solely on batch system data and does not use qq info files.
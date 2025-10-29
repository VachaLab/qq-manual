# qq jobs

The `qq jobs` command is used to display information about a user's jobs. It is qq's equivalent of Infinity's `pjobs`.

***

> **Quick comparison with pjobs**  
> - Unlike `pjobs`, `qq jobs` always shows the nodes that the job is running on, if any are assigned.  
> - Unlike `pjobs`, `qq jobs` distinguishes between failed/killed and successfully finished jobs in its output.

***

### Description

Displays a summary of your jobs or the jobs of a specified user. By default, only unfinished jobs are shown.

```bash
qq jobs [OPTIONS]
```

#### Options

`-u`, `--user` `TEXT` — Username whose jobs should be displayed. Defaults to your own username.

`-a`, `--all` — Include both unfinished and finished jobs in the summary.

`--yaml` — Output job metadata in YAML format.

### Examples

```bash
qq jobs
```

Displays a summary of your unfinished jobs (queued, running, or exiting). This includes both qq jobs and any other jobs associated with the current batch server.

```bash
qq jobs -u user2
```

Displays a summary of user2's unfinished jobs.

```bash
qq jobs --all
```

Displays a summary of all your jobs in the batch system, both unfinished and finished. Note that the batch system eventually removes records of finished jobs, so they may disappear from the output over time.

```bash
qq jobs --yaml
```

Prints a summary of your unfinished jobs in YAML format. This output contains all available metadata as provided by the batch system.

### Notes

- This command lists all types of jobs, including those submitted using `qq submit` and jobs created through other tools.  
- The run times and job states may not exactly match the output of `qq info`, since `qq jobs` relies solely on batch system data and does not use qq info files.


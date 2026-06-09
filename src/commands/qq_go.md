# qq go

The `qq go` command is used to navigate to the working directory of a job. It is qq's equivalent of Infinity's `pgo` when used in an input directory.

***

> **Quick comparison with pgo**  
> - Unlike `pgo`, `qq go` does not have a dual function.  
>   - `pgo` can either open a new shell on the job's main node or navigate to the job's input directory depending on the arguments provided.
>   - `qq go`, on the other hand, always opens a new shell in the job's **working directory** (on the job's main node, if available).
>   - If you want to navigate to the input directory instead, use [`qq cd`](qq_cd.md).
> - If you use `qq go` with a job ID, a new shell in the job's working directory will be opened.
> - `qq go` always attempts to access the job's working directory if it exists, even if the job has failed or been killed — no `--force` option is required.

***

### Description

Opens a new shell in the working directory of the specified qq job, or in the working directories of qq jobs found in the specified directories.

```bash
qq go [OPTIONS] JOB_ID...
```

**JOB_ID...** — One or more IDs of jobs whose working directories should be entered. Optional.

If no JOB_ID and no directory are specified, `qq go` searches for qq jobs in the current directory. If multiple suitable jobs are provided or found, `qq go` opens a shell for each job in turn.

#### Options

- `-d`, `--dir` — One or more directories to search for qq jobs in. Supports globs.

- `-a`, `--all` — Open a shell in the working directories of all your unfinished jobs.

- `-s`, `--server` — Operate on jobs on the specified batch server. If not specified, the current server is used. Only used with `--all`.

### Examples

```bash
qq go 123456
```

Opens a new shell in the working directory of the job with ID `123456` on its main working node. If you use just the numerical portion of the job ID, the job is assumed to be located on the default batch server. If the job is located on a [different batch server](../servers.md#qq-info-qq-go-qq-kill-qq-sync-qq-wipe-qq-respawn), you need to use the full ID including the server address.

If the job does not exist, is not a qq job, its info file is missing, or the working directory no longer exists, the command exits with an error. If the job is not yet running, the command waits until the working directory is ready.

***

```bash
qq go 123456 144844 156432
```

For each of the specified jobs (`123456`, `144844`, `156432`), `qq go` opens a new shell in its working directory.

***

```bash
qq go
```

Opens a new shell in the working directory of the job whose info file is present in the current directory. If multiple suitable jobs are found, `qq go` opens a shell for each job in turn.

***

```bash
qq go -d /path/to/dir
```

Opens a new shell in the working directory of the job whose info file is present in directory `/path/to/dir`. If multiple suitable jobs are found, `qq go` opens a shell for each job in turn.

***

```bash
qq go -d /path/to/job*
```

Opens a new shell in the working directories of all jobs located in directories matching the glob pattern `/path/to/job*` (e.g., `/path/to/job1`, `/path/to/job2`, `/path/to/job3`).

***

```bash
qq go --all
```

Opens a new shell in the working directories of all your uncompleted (i.e, running and queued) qq jobs in turn.

***

```bash
qq go --all --server meta
```

Opens a new shell in the working directories of all your uncompleted qq jobs associated with the Metacentrum batch server.

### Notes

- Uses `cd` for local directories or `ssh` for remote hosts.
- Does not change the working directory of the current shell; it always opens a new shell at the destination.

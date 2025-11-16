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

Opens a new shell in the working directory of the specified qq job, or in the working directory of the job submitted from the current directory.

```bash
qq go [OPTIONS] JOB_ID
```

**JOB_ID** — Identifier of the job whose working directory should be entered. This argument is optional.

If `JOB_ID` is not provided, `qq go` searches for qq jobs in the current directory. If multiple matching jobs are found, `qq go` opens a shell for each one in turn.

### Examples

```bash
qq go 123456
```

Opens a new shell in the working directory of the job with ID `123456` on its main working node. You can use either the short job ID or the full ID including the batch server address. If the job does not exist, is not a qq job, its info file is missing, or the working directory no longer exists, the command exits with an error. If the job is not yet running, the command waits until the working directory is ready.

```bash
qq go
```

Opens a new shell in the working directory of the job whose info file is present in the current directory. If multiple suitable jobs are found, `qq go` opens a shell for each job in turn.

### Notes

- Uses `cd` for local directories or `ssh` for remote hosts.
- Does not change the working directory of the current shell; it always opens a new shell at the destination.
# qq kill

The `qq kill` command is used to terminate qq jobs. It is qq's equivalent of Infinity's `pkill`.

***

> **Quick comparison with pkill**
> - You can use `qq kill` with a job ID to terminate a job without having to navigate to its input directory. You can even provide multiple IDs to kill multiple jobs at once.
> - You can provide `qq kill` with one or more directories to search for qq jobs in. All discovered jobs are then killed.
> - When prompted to confirm that you want to terminate a job, `qq kill` only requires pressing a single key (`y` to confirm or any other key to cancel), instead of typing 'yes' and pressing Enter.
> - `qq kill --force` will attempt to terminate jobs even if qq considers them finished, failed, or already killed. This is useful for removing stuck or lingering jobs from the batch system.

***

### Description

Terminates the specified qq jobs or all qq jobs in the specified directories.

```bash
qq kill [OPTIONS] JOB_ID...
```

**JOB_ID...** — One or more IDs of jobs to terminate. Optional.

If no `JOB_ID` and no directory are provided, `qq kill` searches for qq jobs in the current directory. If multiple suitable jobs are provided or found, `qq kill` terminates each one in turn.

By default, `qq kill` prompts for confirmation before terminating each job.

Without the `--force` flag, it will only attempt to terminate jobs that are queued, held, booting, or running — not jobs that are already finished or killed. When the `--force` flag is used, `qq kill` attempts to terminate any job regardless of its state, including jobs that qq believes are already finished or killed. This can be used to remove lingering or stuck jobs.

#### Options
- `-d`, `--dir` — One or more directories to search for qq jobs in. Supports globs.

- `-a`, `--all` — Terminate all your unfinished jobs.
 
- `-s`, `--server` — Terminate jobs on the specified batch server. If not specified, the current server is used. Only used with `--all`.

- `-y`, `--yes` — Terminate the job(s) without asking for confirmation.

- `--force` — Forcefully terminate the job(s), ignoring its current state and skipping confirmation.

### Examples

```bash
qq kill 123456
```

Terminates the job with ID `123456` located on the default batch server. If the job is located on a [different batch server](../servers.md#qq-info-qq-go-qq-kill-qq-sync-qq-wipe-qq-respawn), you need to use the full ID including the sever address.

Upon running this command, you will be prompted to confirm the termination by pressing `y`. This command only works if the specified job is a qq job with a valid and accessible info file, and the target batch server is reachable from the current machine.

***

```bash
qq kill 123456 144844 156432
```

Terminates jobs `123456`, `144844`, and `156432`. You will be asked to confirm each termination individually.

***

```bash
qq kill
```

Terminates all suitable qq jobs whose info files are present in the current directory. You will be asked to confirm each termination individually.

***

```bash
qq kill 123456 -y
```

Terminates the job with ID `123456` without asking for confirmation (assumes 'yes').

***

```bash
qq kill 123456 --force
```

Forcefully terminates the job with ID `123456`. This kills the job immediately and without confirmation, regardless of qq's recorded job state.

***

```bash
qq kill -d /path/to/dir
```

Terminates the job which info file is located in the directory `/path/to/dir`.

***

```bash
qq kill -d /path/to/job*
```

Terminates all suitable jobs which info files are located in directories matching the glob pattern `/path/to/job*` (e.g., `/path/to/job1`, `/path/to/job2`, `/path/to/job3`). Useful for terminating job collections.

***

```bash
qq kill --all
```

Terminates all your qq jobs.

***

```bash
qq kill --all --server meta
```

Terminates all your uncompleted qq jobs associated with the Metacentrum batch server.

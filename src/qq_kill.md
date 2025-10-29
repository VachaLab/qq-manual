# qq kill

The `qq kill` command is used to terminate qq jobs. It is qq's equivalent of Infinity's `pkill`.

***

> **Quick comparison with pkill**
> - You can use `qq kill` with a job ID to terminate a job without having to navigate to its input directory.
> - When prompted to confirm that you want to terminate a job, `qq kill` only requires pressing a single key (`y` to confirm or any other key to cancel), instead of typing 'yes' and pressing Enter.
> - `qq kill --force` will attempt to terminate jobs even if qq considers them finished, failed, or already killed. This is useful for removing stuck or lingering jobs from the batch system.

***

### Description

Terminates the specified qq job, or all qq jobs submitted from the current directory.

```bash
qq kill [OPTIONS] JOB_ID
```

**JOB_ID** — Identifier of the job to terminate. This argument is optional.

If `JOB_ID` is not provided, `qq kill` searches for qq jobs in the current directory. If multiple suitable jobs are found, `qq kill` terminates each one in turn.

By default, `qq kill` prompts for confirmation before terminating a job.

Without the `--force` flag, it will only attempt to terminate jobs that are queued, held, booting, or running — not jobs that are already finished or killed. When the `--force` flag is used, `qq kill` attempts to terminate any job regardless of its state, including jobs that qq believes are already finished or killed. This can be used to remove lingering or stuck jobs.

#### Options

`-y`, `--yes` — Terminate the job without asking for confirmation.

`--force` — Forcefully terminate the job, ignoring its current state and skipping confirmation.

### Examples

```bash
qq kill 123456
```

Terminates the job with ID `123456`. You can use either the short job ID or the full ID including the batch server address. You will be prompted to confirm the termination by pressing `y`. This command only works if the specified job is a qq job with a valid and accessible info file, and the batch server must be reachable from the current machine.

```bash
qq kill
```

Terminates all suitable qq jobs whose info files are present in the current directory. You will be asked to confirm each termination individually.

```bash
qq kill 123456 -y
```

Terminates the job with ID `123456` without asking for confirmation (assumes 'yes').

```bash
qq kill 123456 --force
```

Forcefully terminates the job with ID `123456`. This kills the job immediately and without confirmation, regardless of qq's recorded job state.
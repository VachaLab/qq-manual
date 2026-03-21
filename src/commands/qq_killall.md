# qq killall

The `qq killall` command is used to terminate all of your qq jobs. It is qq's equivalent of Infinity's `pkillall`.

***

> **Quick comparison with pkillall**
> - `qq killall` can only terminate jobs submitted using `qq submit`; other jobs are not affected.

***

### Description

Terminates all qq jobs submitted by the current user.

```bash
qq killall [OPTIONS]
```

This command only terminates qq jobs — other jobs in the batch system are not affected.

By default, `qq killall` prompts for confirmation before terminating the jobs.

#### Options

`-y`, `--yes` — Terminate all jobs without confirmation.

`--force` — Forcefully terminate all jobs, ignoring their current states and skipping confirmation.

### Examples

```bash
qq killall
```

Terminates all your qq jobs with valid and accessible info files. You will be prompted to confirm termination by pressing `y`.

```bash
qq killall -y
```

Terminates all your qq jobs with valid and accessible info files without asking for confirmation (assumes "yes").

```bash
qq killall --force
```

Forcefully terminates all your qq jobs with valid and accessible info files. No confirmation is requested, and the jobs will be terminated even if qq believes they are already finished, failed, or killed.
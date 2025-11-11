# qq wipe

The `qq wipe` command is used to delete working directories of qq jobs. It has no direct equivalent in Infinity.

> It can be tricky to remember the difference between `qq wipe` and [`qq clear`](qq_clear.md). A helpful reminder: **W**ipe affects the **W**orking directory.

### Description

Deletes the working directory of the specified qq job, or of all qq jobs in the current directory.

```bash
qq wipe [OPTIONS] JOB_ID
```

**JOB_ID** — Identifier of the job which working directory should be deleted. This argument is optional.

If JOB_ID is not specified, `qq wipe` searches for qq jobs in the current directory.

By default, `qq wipe` prompts for confirmation before deleting the working directory. 

Without the `--force` flag, it will only attempt to delete working directories of jobs that have failed or been killed. When the `--force` flag is used, `qq wipe` attempts to wipe the working directory of any job regardless of its state, including jobs that are queued, running or successfully finished. You should be very careful when using this option as it may delete useful data or cause your job to crash!

> If the working directory matches the input directory, `qq wipe` will never delete it, even if you use the `--force` flag, to protect you from accidentally removing your data.


#### Options

`-y`, `--yes` — Delete the working directory without confirmation.

`--force` — Delete the working directory of the job forcibly, ignoring its current state and without confirmation.

### Examples

```bash
qq wipe 123456
```

Deletes the working directory of the job with ID `123456`. You can use either the short job ID or the full ID including the batch server address. You will be prompted to confirm the termination by pressing `y`. This command only works if the specified job is a qq job with a valid and accessible info file, and the batch server must be reachable from the current machine.

```bash
qq wipe
```

Deletes the working directories of all suitable qq jobs whose info files are present in the current directory. You will be asked to confirm each deletion individually.

```bash
qq wipe 123456 -y
```

Deletes the working directory of the job with ID `123456` without asking for confirmation (assumes 'yes').

```bash
qq wipe 123456 --force
```

Forcefully deletes the working directory of the job with ID `123456`. This deletes the working directory no matter the state of the job. **This is dangerous — only use the `--force` flag if you are absolutely sure you know what you are doing!**

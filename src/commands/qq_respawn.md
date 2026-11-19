# qq respawn

The `qq respawn` command is used to "respawn" jobs, i.e. put failed or killed jobs back into the queue to be retried. It has no direct equivalent in Infinity.

> Imagine you find that your job has failed because it unexpectedly reached a walltime limit (e.g., because it was running on a slow node). You want to just put the job back into the queue to be retried. Normally, you would run something like the following sequence of commands:
>
> ```bash
> # go to the directory with the crashed job
> qq cd <crashed-job-id>
> # remove the working directory (optional)
> qq wipe
> # clear the runtime files from the crashed job
> qq clear
> # submit the job to the queue with the same parameters
> qq submit -q <queue> --ncpus 8 --ngpus 1 --walltime 1d <script-name>
> ```
>
> With `qq respawn`, you can just run:
>
> ```bash
> # remove the working directory, clear runtime files, 
> # and submit a new job with the original parameters
> qq respawn <crashed-job-id>
> ```

### Description

Respawns the specified qq jobs, or all qq jobs in the specified directories.

```bash
qq respawn [OPTIONS] JOB_ID...
```

**JOB_ID...** — One or more IDs of jobs to respawn. Optional.

If no `JOB_ID` and no directory are provided, `qq respawn` searches for qq jobs in the current directory. If multiple suitable jobs are found, `qq respawn` respawns each one in turn.

#### Options

- `-d`, `--dir` — One or more directories to search for qq jobs in. Supports globs.
- `--force-continue` — If the archive of a loop job already contains files for the next cycle, continue with the next cycle without asking.
- `--force-rerun` — If the archive of a loop job already contains files for the next cycle, rerun the failed cycle without asking.

### Examples

```bash
qq respawn 123456
```

Respawns the job with ID `123456` located on the default batch server. If the job is located on a [different batch server](../servers.md#qq-info-qq-go-qq-kill-qq-sync-qq-wipe-qq-respawn), you need to use the full ID including the sever address.

Only failed and killed jobs can be respawned. If you try to respawn a job in any other state, you will get an error.

***

```bash
qq respawn 123456 144844 156432
```

Respawns jobs `123456`, `144844`, and `156432`, if they are suitable.

***

```bash
qq respawn
```

Respawns all suitable qq jobs whose info files are present in the current directory (typically one, since `qq` requires one job per directory).

****

```bash
qq respawn -d /path/to/dir
```

Respawns all suitable qq jobs whose info files are present in direcotry `/path/to/dir`.

***

```bash
qq respawn -d /path/to/job*
```

Respawns all suitable qq jobs whose info files are located in directories matching the glob pattern `/path/to/job*` (e.g., `/path/to/job1`, `/path/to/job2`, `/path/to/job3`). Useful for managing job collections.

### Respawning loop jobs

When respawning a loop job, `qq respawn` checks the archive. Usually, it corresponds to the failed cycle, and this cycle is run again. However, if the cycle finished and archived its files but the job failed afterwards (typically because the next cycle could not be submitted), the archive already contains files for the next cycle. In that case, `qq respawn` asks whether to continue with the next cycle or to rerun the failed one. If you continue, the qq runtime files of the failed cycle are archived like those of any other finished cycle, and its working directory is kept if it still exists, since it may contain files that were not copied back or archived.

If the failed cycle was the last cycle of the loop job, it can only be rerun.

To choose without being asked, for example when respawning many jobs at once, use the `--force-continue` or `--force-rerun` option.

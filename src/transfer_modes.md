# Transferring files from the working directory

> This is an advanced topic dealing with working directories. You can very likely rely on qq's default behavior and ignore this section completely.

As described in various sections of this manual, if your job creates its own [working directory](work_dir.md) (e.g., on scratch), the data produced during the job's execution are transferred back to the input directory **only if the job finishes successfully** (with exit code `0`, or the value of the `QQ_NO_RESUBMIT` environment variable in the case of loop/continuous jobs). The working directory is then removed and can no longer be accessed.

If the job fails (i.e., finishes with an exit code other than `0`), no data are transferred to the input directory (except for [qq runtime files](runtime_files.md)). Instead, the files remain in the working directory. You can then navigate to the working directory to determine what went wrong using [`qq go`](qq_go.md), fetch the data manually using [`qq sync`](qq_sync.md), or delete the working directory using [`qq wipe`](qq_wipe.md).

In some situations, it may be useful to automatically transfer files even from failed jobs. In other cases, you may want the opposite behavior—never transfer files automatically and always keep them in the working directory. To support these use cases, qq allows you to specify a **transfer mode** when submitting a job. The transfer mode determines how file transfer should be handled.

For example, to always transfer files from the working directory, submit a job with the `--transfer-mode always` option:

```bash
qq submit -q default --ncpus 8 --walltime 1d --transfer-mode always my_script.sh
```

With this setting, files are transferred from the working directory to the input directory regardless of whether the job succeeds or fails. **The working directory is then removed.**

> Files are **never** transferred if the job is **killed** by you, the administrator, or the system, regardless of the specified transfer mode. Similarly, in the case of a qq error (job exit codes 90–99), data may not be transferred even if explicitly requested. In these situations, you can still access the data using [`qq go`](qq_go.md) or [`qq sync`](qq_sync.md), unless the working directory has already been deleted.

> Specifying a transfer mode only makes sense when the working directory is different from the input directory (i.e., when you are **not** using the `--work-dir input_dir` option). If the job runs directly in the input directory, no file transfer is performed.

## Transfer modes

Transfer modes can be divided into **keyword transfer modes** and **numerical transfer modes**.

There are four keyword transfer modes:

- `success`  
  The **default** mode. If you do not specify a transfer mode, this one is used. Files are transferred from the working directory only if the job finishes successfully (exit code `0`).

- `failure`  
  Files are transferred from the working directory only if the job fails (exit code not equal to `0`).

- `always`  
  Files are transferred from the working directory regardless of the job's exit code. **Files are not transferred if the job is killed.**

- `never`  
  Files are **never** transferred from the working directory, regardless of the job's exit code. All files remain in the working directory, which is therefore not removed.

Numerical transfer modes specify the exact exit code that the job must finish with for the files to be transferred. For example, transfer mode `0` means that files are transferred only if the job exits with code `0`—in other words, transfer mode `0` is equivalent to `success`. Transfer mode `1` transfers files only if the exit code is `1`, and transfer mode `42` transfers files only if the exit code is `42`.

This allows you to specify precisely which exit conditions should trigger file transfer from the working directory, especially when combined with the [feature described below](#specifying-multiple-transfer-modes).

### Specifying multiple transfer modes

You can specify multiple transfer modes for [`qq submit`](qq_submit.md) by separating them with commas, colons, or spaces. If the condition for **any** of the specified transfer modes is satisfied, the files are transferred and the working directory is removed.

For example, to transfer files if the job finishes successfully or with exit code `3` or `4`:

```bash
qq submit -q default (...) --transfer-mode success,3,4 my_script.sh
```

## Archive modes

Transfer mode does not affect how files are archived in [loop jobs](loop_job.md). For example, if you run a loop job with `--transfer-mode always` and the job fails, **archiving is not performed** and **all files from the working directory are transferred to the input directory**.

If you want files to be archived even when a loop job fails, you must also specify `--archive-mode`. For example:

```bash
qq submit -q default (...) --transfer-mode always --archive-mode always qq_loop_md
```

The same modes are available for `--archive-mode` as for `--transfer-mode` (see [above](#transfer-modes)).

> Remember that all qq options, including `--transfer-mode` and `--archive-mode`, can also be specified [inside the submitted script](qq_submit.md#specifying-options-in-the-script) using qq directives. You can also set the default transfer and archive mode for your jobs in the [QQ_CONFIG file](config.md) (this needs to be done on all machines from which you submit jobs).
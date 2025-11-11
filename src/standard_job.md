# Standard jobs

A standard job is the default qq job type. This section describes the full lifecycle of a standard qq job.

## 1. Submitting the job

Submitting a qq job is done using [`qq submit`](qq_submit.md).

`qq submit` submits the job to the batch system and generates a qq info file containing metadata and details about the job. This info file is named after the submitted script, has the `.qqinfo` extension, and is located in the input directory (often also called the submission or, somewhat confusingly, job directory).

Once submitted, the batch system takes over, finding a suitable place and time to execute your job. As a user, you don't need to do anything else except wait for the job to run.

## 2. Preparing the working directory

When the batch system allocates a machine for your job, the [`qq run`](qq_run.md) environment takes over. It first prepares a working directory for the job on the execution node.

If you requested the job to run in the input directory (by submitting with `--workdir=input_dir` or the equivalent `--workdir=job_dir`), the input directory is used directly as the working directory, and no additional setup is required.

If you requested the job to run on scratch (the default for PBS), a working directory is created inside your allocated scratch space, and **all files and directories in the input directory** are copied there — except for the qq info file and the "archive" directory (discussed [later](loop_job.md#archive-directory)).  
During submission, you can also specify files you explicitly do **not** want to copy to the working directory.

Once the working directory is ready, qq updates the info file to mark the job state as `running`. Only then is your submitted script executed.

## 3. Executing the script

Your submitted script is then executed using `bash`.

The script should exit with code `0` if everything ran successfully, or a non-zero code to indicate an error. The exit code is passed back to qq, which sets the appropriate job state (`finished` for 0, `failed` for anything else).

Standard output from your script is saved to a file named after your script with the `.out` extension. Standard error output is stored in a similar file with the `.err` extension.

## 4. Finalizing execution

After the script finishes, qq performs cleanup.

If your job ran in the input directory, cleanup is simple: qq updates the job's state (`finished` or `failed`) in the qq info file, and execution ends.

If your job ran on scratch, cleanup depends on the script's exit status.

If it finished successfully, all files from the working directory are copied back to the input directory, and the working directory (or its contents) is deleted. Finally, qq sets the job state to `finished`.

If the job failed, the working directory is left intact on the execution machine for inspection (you can open it using [`qq go`](qq_go.md), download it using [`qq sync`](qq_sync.md), or delete it using [`qq wipe`](qq_wipe.md)). The job state is set to `failed`.

Regardless of the result, qq creates an output file (named after your script with the `.qqout` extension) in the input directory. This file contains basic information about what qq did and when the job finished. Depending on the batch system, this file may appear either after job completion (PBS) or immediately after the job starts being executed (Slurm).

## Killing a qq job

If your job is killed (either manually via [`qq kill`](qq_kill.md) or automatically by the batch system, for example if it exceeds walltime), all files remain in the working directory on the execution machine. qq stops the running script and marks the job state as `killed`.

## Submitting the next job

After a job has completed successfully, you may want to submit a new one: for example, to proceed to the next stage of your workflow. If you try to submit another qq job from the same directory as the previous one, you will however encounter an error:

```
ERROR       Detected qq runtime files in the submission directory. Submission aborted.
```

This behavior is intentional. qq enforces a one-job-per-directory policy to promote reproducibility and maintain organized workflows. Each job should reside in its own dedicated directory.

If your previous job crashed or was terminated and you wish to rerun it, you can remove the existing qq runtime files using [`qq clear`](qq_clear.md).

## Additional notes

- Most operations during working directory setup and cleanup are automatically retried in case of errors. This helps prevent job crashes caused by temporary storage or network issues. If an operation fails, qq waits a few minutes and retries — up to three attempts. After three failures, qq stops and reports an error. Note that qq does **not** retry execution of your script itself.
- If your job fails with an exit code between 90–99, this usually means a qq operation failed. Check the qq output file (`.qqout`) for more details. An exit code of 99 indicates a critical or unexpected error, which usually means a bug in qq. Please report such cases.
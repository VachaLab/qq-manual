# Environment variables

When a qq job is submitted, several environment variables are automatically set and can be used within the submitted script.

- `QQ_ENV_SET`: indicates that the job is running inside the qq environment (always set to `true`)  
- `QQ_INPUT_MACHINE`: name of the input machine from which the job was submitted
- `QQ_INPUT_DIR`: absolute path to the job's input directory on the input machine
- `QQ_INFO`: absolute path to the qq job's info file on the input machine
- `QQ_BATCH_SYSTEM`: name of the batch system used to schedule and execute the job

If the `QQ_DEBUG` environment variable is set when running `qq submit`, its value is propagated to the job environment as well. This turns on the debug mode, dramatically increasing the verbosity of [`qq run`](qq_run.md).

If the job is a [loop job](loop_job.md), the following additional environment variables are also set:

- `QQ_LOOP_CURRENT`: current cycle number of the loop job
- `QQ_LOOP_START`: first cycle of the loop job
- `QQ_LOOP_END`: last cycle of the loop job
- `QQ_ARCHIVE_FORMAT`: filename format used for archived files
- `QQ_NO_RESUBMIT`: exit code that can be returned from the body of the script to indicate that the next cycle of the job should [not be submitted](loop_job.md#forcing-qq-to-not-resubmit)

> Apart from the variables listed here and those provided by the batch system itself, no other environment variables are typically propagated from the submission environment to the job environment.

Additional internal environment variables may be set, but these are not intended for public use and may change or be removed in future versions of qq.
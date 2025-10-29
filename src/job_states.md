# Job states

There are three types of job states that qq uses: **batch states**, **naïve states**, and **real states**.
- *Batch states* describe the job's state according to the batch system itself.
- *Naïve states* are recorded in qq info files.
- *Real states* combine both sources of information to report the most accurate job status.

Batch states are shown in the output of [`qq jobs`](qq_jobs.md) and [`qq stat`](qq_stat.md), while real states are used by all other commands that report a job's status.

Below are the meanings of the most common real states you may encounter:

- **queued** – The job has been submitted and is waiting in the queue for execution.
- **held** – The job has been submitted but is blocked from execution for some reason.
- **booting** – The job has been allocated computing nodes and the working directory is being prepared, but it is not yet ready.
- **running** – The job is currently running; its script is being executed or the execution is being finalized.
- **exiting** – `qq run` has finished executing or is submitting the next job cycle (for loop jobs), but the batch system hasn't completed the job yet and the `.qqout` file isn't available.
- **finished** – The job completed successfully (exit code 0), has been synchronized, and the `.qqout` file is available in the input directory.
- **failed** – The job's execution failed (exit code > 0).
- **killed** – The job was terminated by the user, an administrator, or the batch system.
- **in an inconsistent state** – qq believes the job to be in a specific state which is incompatible with what the batch system reports. This usually indicates either a bug or that the job was manipulated outside qq.
- **unknown** – The job is in a state that qq does not recognize.
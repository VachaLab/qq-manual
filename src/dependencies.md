# Specifying job dependencies

Occasionally, you may want your submitted job to start only after some condition related to the state of some other job(s) is fulfilled. You can control when the job should start being executed using the `--depend` option of [`qq submit`](commands/qq_submit.md).

As the value for this option, you can provide a list of comma- or space-separated job dependencies in this format:
```text
<dependency_type>=<job_id>[:<job_id>...]
```

A job that is waiting for its dependencies to be satisfied is in a [held state](job_states.md).

## Dependency types

The dependency type specifies the condition that the given jobs need to fulfill for the newly submitted job to start. There are currently four supported types:

- `after` — the submitted job can start only after the specified job has **started**
- `afterok` — the submitted job can start only after the specified job has **finished successfully**
- `afternotok` — the submitted job can start only after the specified job has **failed or been killed**
- `afterany` — the submitted job can start only after the specified job has **completed** (either successfully, unsuccessfully, or been killed)

## Specifying multiple job IDs

For a single dependency type, you can provide either a single job ID or multiple colon-separated job IDs. For example, `after=412643` means that the job with ID 412643 needs to start before the submitted job can start, while `after=412643:412644:412645` means that all three specified jobs need to start before the submitted job can start.

## Specifying multiple dependency types

If your job should start only after multiple different conditions are fulfilled, you can provide multiple dependency expressions separated by commas or spaces. For example:

```bash
qq submit (...) --depend after=412643,afterok=412777:412779
```

means that the submitted job can start only after all of the following is true:
- the job 412643 has started,
- the jobs 412777 and 412779 have finished successfully.
# Continuous jobs

Continuous jobs are jobs that automatically submit their continuation at the end of execution, but unlike [loop jobs](loop_job.md), do not track their current cycle and do not perform archiving or any other advanced operations.

They simply continue running and submitting their continuations until resubmission is explicitly stopped by returning the value of the environment variable [`QQ_NO_RESUBMIT`](#forcing-a-continuous-job-not-to-resubmit) from the executed script, or until the job is manually killed or fails.

To submit a continuous job, run:

```bash
qq submit (...) --job-type continuous
```

## Submitting and running a continuous job

When submitting a continuous job, the sequence of operations is initially the same as for a [standard job](standard_job.md). The job gets queued by the batch system, then starts, creates a working directory, copies the data from the input directory there, and executes the submitted script. 

Once the script successfully finishes, the data are transferred back from the working directory to the input directory. Then the job is automatically resubmitted using the same submission options. The new job has exactly the same name as the previous job, meaning that **runtime files for the next job overwrite those created for the previous job**. Once the *next* job finishes, it also automatically submits its continuation, and this continues indefinitely until the job fails or is killed. Failed and killed jobs are not resubmitted.

> Continuous jobs do not perform any archiving operations and overwrite their runtime files. If you want to or need to keep runtime files for all your finished jobs, use a [loop job](loop_job.md) instead.

## Forcing a continuous job not to resubmit

An infinitely running job may be in some cases useful, but it is typically more useful to be able to stop a job from submitting its own continuation when some specific condition is reached.

To do this, you can use the same mechanism as for [loop jobs](loop_job.md#forcing-qq-not-to-resubmit)—namely, by returning the value of the environment variable `QQ_NO_RESUBMIT` from within the executed script:

```bash
#!/usr/bin/env -S qq run

# qq job-type continuous

...

# if a specific condition is met, do not resubmit but finish successfully
if [ -n "${SOME_CONDITION}" ]; then
    exit "${QQ_NO_RESUBMIT}"
fi

exit 0
```

If qq detects this exit code (`${QQ_NO_RESUBMIT}`), it will not submit the continuation of the continuous job. The current run will still be marked as successfully finished (exit code 0).

## Extending a continuous job

In some cases, you may want to prolong a continuous job that has successfully finished (and that has been forced not to submit its own continuation). As with [loop jobs](loop_job.md#extending-a-loop-job), you do not need to delete the runtime files. You can simply run [`qq submit`](qq_submit.md) again—qq will recognize that a continuous job has been running in the directory and will allow submitting the job. The extended job will continue running and submitting its continuation until its script returns the environment variable [`QQ_NO_RESUBMIT`](#forcing-a-continuous-job-not-to-resubmit) or until the job fails or is killed.

> Using continuous jobs is **not recommended** for long-running simulations that generate large amounts of data. 
> 
> When using local scratch as your working directory (the default), qq copies all files from the job's input directory to the working directory. If you do not archive your generated data (such as MD trajectories), **everything your simulation has generated** will be copied to the working directory in each job cycle, which can consume significant time and disk space (you may easily exceed the default storage quota allocated for your working directory on scratch).
> 
> If possible, use [loop jobs](loop_job.md) instead, as they support data archiving. For Gromacs simulations, qq provides [qq_loop](run_scripts.md) scripts for running long simulations.
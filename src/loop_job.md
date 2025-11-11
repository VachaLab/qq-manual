# Loop jobs

Loop jobs are jobs that automatically submit their continuation at the end of execution. This section describes how they differ from standard jobs. Please read the section about [standard jobs](standard_job.md) first — otherwise, this may be difficult to follow.

To turn a job into a loop job, you must set two [`qq submit`](qq_submit.md) options:  
- `job-type` to `loop`, and  
- `loop-end` to specify the last [cycle](#loop-job-cycles) of the loop job.

## Loop job cycles

Each loop job consists of multiple cycles. Every cycle is a separate job from the batch system's perspective. Before a cycle finishes, it submits the next one and then ends. The next cycle continues where the previous one left off.

You can control the starting cycle using the `loop-start` submission option (defaults to 1). To set the final cycle, use the `loop-end` option. The cycle specified as `loop-end` will be the last one executed.

## Archive directory

Each loop job creates an archive directory inside the input directory. This directory is **not** copied to the job's working directory, so it can safely hold large amounts of data. In loop jobs, the archive serves two main purposes:

- to identify and initialize the current cycle of the loop job,  
- to store data from previous cycles without copying them to the working directory.

You can control the archive directory's name using the `archive` submission option (default: `storage`).

Archived files should follow a specific filename format that includes the job cycle number they belong to. You can define this format using the `archive-format` submission option (default: `job%04d`). In this format, `%04d` is replaced by the cycle number — for example, `job0001` for cycle 1, `job0002` for cycle 2, `job0143` for cycle 143 and so on.

When a new cycle is submitted (either manually or automatically by the previous one), qq sets the current cycle number based on the highest cycle number found in the names of the archived files. **In other words: in each cycle of a loop job, at least one file must be added to the archive whose name includes the number of the next cycle**. Otherwise, the job submission will fail with an error.

(If no archive directory or archived files exist, the cycle number defaults to `loop-start`.)

## Working with the archive

You typically should not transfer files from and to the archive directly inside your submitted script. If you follow the proper naming etiquette, the [`qq run`](qq_run.md) environment will handle all archiving operations for you.

At the start of each cycle, after copying files from the input directory to the working directory, `qq run` checks the archive and automatically copies all files associated with the **current cycle** into the working directory. For example, if the current cycle number is 8 and `archive-format` is `job%04d`, any file in the archive containing `job0008` in its name will be automatically copied to the working directory. These files can then be used to initialize the next cycle of the job.

After the submitted script finishes successfully, qq moves **all** files matching the `archive-format` (for any cycle) to the archive directory. For example, if the 8th cycle produces the files `job0008.txt`, `job0008.dat`, and `job0009.init`, and the archive format is `job%04d`, all three files will be moved to the archive. Only after these files are archived are the remaining files in the working directory moved to the input directory. This ensures that archived files don't clutter the input directory or get copied to the next cycle's working directory.

In summary, unlike with Infinity, you do not need to explicitly fetch files from and to the archive, you just need to name them accordingly and qq will archive them automatically.

If the script fails or the job is killed, no archival is performed. All files remain in the working directory, as with standard jobs.

## Resubmiting

After the current cycle finishes the execution of the submitted script, archives the relevant files, and copies the other files to the input directory, qq resubmits the job. This means that the next cycle is submitted from the original input directory. The resubmission may occur from either the original input machine or the current main execution node, depending on the batch system.

The new job (the next cycle) waits for the previous one to finish completely before starting. When it begins, even before creating its working directory, qq archives runtime files from the previous cycle, renaming them according to the specified `archive-format`.

If the current cycle of the loop job corresponds to `loop-end`, no resubmission is performed.

## Extending a loop job

Sometimes, after a job completes *N* cycles, you may realize you need *M* more. To extend the job, simply submit it again from the same input directory with `loop-end` set to *N + M*, either on the command line or in the submission script.  

**Importantly:** you do not need to [delete any runtime files](standard_job.md#submitting-the-next-job) from the previous cycle — and you probably shouldn't. `qq submit` can detect that you are extending an existing loop job and will handle the continuation correctly. This has the added benefit that the runtime files from the Nth cycle will be properly archived.

## Forcing qq to not resubmit

You can manually force qq to **not** submit the next cycle of a loop job, even if the current cycle number has not yet reached `loop-end`, by returning the value of the environment variable `QQ_NO_RESUBMIT` from within the script:

```bash
#!/usr/bin/env -S qq run

# qq job-type loop
# qq loop-end 100
# qq archive storage
# qq archive-format md%04d

...

# if a specific condition is met, do not resubmit but finish successfully
if [ -n "${SOME_CONDITION}" ]; then
    exit "${QQ_NO_RESUBMIT}"
fi

exit 0
```

If qq detects this exit code, it will not submit the next cycle of the loop job. The current cycle will still be marked as successfully finished (exit code 0).
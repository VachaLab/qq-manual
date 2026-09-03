# Writing loop job scripts

Even if you have read the section about [loop jobs](job_types/loop_job.md), you may still be unsure how to actually write a loop job script. This tutorial goes through it slowly, starting from an ordinary script and adding one piece at a time, so that you understand the key concepts.

## What are loop jobs for?

To explain the basic ideas, we will work with a fictional computational program called `sick`, as in Super Important Calculation Kit. Assume that `sick` is already installed on the compute nodes and can perform some heavy calculations.

`sick` requires an input file with calculation options, `settings.toml`, which among other things says how many calculation steps should be performed. `sick` can also accept a "state" file, `input.state`, describing the current state of the calculation. While running, `sick` continuously writes the results into `results.dat`, and at the end it writes an output state file, `output.state`. During the run it also prints logs to the console, specifically to `stdout`, which means they are captured in the [`.out` runtime file](runtime_files.md#out-files).

A qq run script for `sick` could look like this:

```bash
#!/usr/bin/env -S qq run

sick -s settings.toml -r results.dat -o output.state
```

That is all fine, but `sick` is very computationally expensive and a single calculation takes a very long time to finish. Several weeks or months is not unusual. That is a problem for two reasons. First, the longer the walltime you request, the longer you tend to wait in the queue. And imagine waiting a week for your job to start running only to realize that you have messed up an option in the settings file and have to start over. Second, some clusters (robox, for instance) do not allow such long jobs at all; the limit there is one or two days.

But consider this: `sick` writes an output state file that can be used to continue the calculation from where it left off, and a lot of real software can do the same. This means that you can split your calculation into smaller chunks, run each chunk as its own shorter job, and use the output state file of one job as the input of the next one.

You could do all of this by hand, waiting for one job to finish and then submitting the next one yourself. That is tedious, especially if you run many calculations at once. qq offers a way to automate it.

## Writing a continuous job script

Let's split the calculation into chunks and run each chunk as its own job. First, the script:

```bash
#!/usr/bin/env -S qq run

# if there is an input state file,
# use it to continue the calculation
if [ -f input.state ]; then
    sick -i input.state -s settings.toml -r results.dat -o output.state
# if there is no input state file, start from scratch
else
    sick -s settings.toml -r results.dat -o output.state
fi

# rename the output state file to input.state
# to use it in the following job
mv output.state input.state
```

The script checks whether an `input.state` file exists and uses it to continue the calculation, or starts from scratch if there is none. At the end of the job, it renames the output state file to `input.state` so that the next job picks it up. The only thing left is to tell qq that this is a continuous job. (Yes, a continuous job. Don't worry, we will get to loop jobs later.) We add the following right after the shebang line:

```bash
# qq job-type continuous
```

When you submit this job, it will run and then submit itself again right before finishing. The next job will do the same, and so will the one after that, and so on, forever.

> [!NOTE]
> A continuous job has no built-in end. Unless the script tells qq to stop, or you stop the job yourself with `qq kill`, it will keep resubmitting itself indefinitely.

There are also two other problems. `sick` overwrites `results.dat` in every job, so we lose the results of all previous cycles, and we lose the state file of each cycle as well, because it is overwritten in the next one.

So we need to store the results and the state files, and we need to track the current cycle, so that we can stop the job when we want to.

## Manual cycle tracking and storing results

Let's start with cycle tracking, which we need for storing the results anyway.

We can keep the current cycle number in a file called `cycle`.

```bash
#!/usr/bin/env -S qq run

# qq job-type continuous

# if the cycle file exists, increment the cycle number
if [ -f cycle ]; then
    CYCLE=$(($(cat cycle) + 1))
# otherwise start from 1
else
    CYCLE=1
fi
echo ${CYCLE} > cycle

# if there is an input state file,
# use it to continue the calculation
if [ -f input.state ]; then
    sick -i input.state -s settings.toml -r results.dat -o output.state
# if there is no input state file, start from scratch
else
    sick -s settings.toml -r results.dat -o output.state
fi

# rename the output state file to input.state
# to use it in the following job
mv output.state input.state
```

The cycle number is now stored in the `cycle` file. What can we do with it?

For a start, we can use it to stop the job.

```bash
#!/usr/bin/env -S qq run

# qq job-type continuous

N_CYCLES=10

# if the cycle file exists, increment the cycle number
if [ -f cycle ]; then
    CYCLE=$(($(cat cycle) + 1))
# otherwise start from 1
else
    CYCLE=1
fi
echo ${CYCLE} > cycle

# if there is an input state file,
# use it to continue the calculation
if [ -f input.state ]; then
    sick -i input.state -s settings.toml -r results.dat -o output.state
# if there is no input state file, start from scratch
else
    sick -s settings.toml -r results.dat -o output.state
fi

# rename the output state file to input.state
# to use it in the following job
mv output.state input.state

# if the cycle limit is reached, stop the job
if [ ${CYCLE} -ge ${N_CYCLES} ]; then
    echo "Cycle limit reached. Stopping."
    exit ${QQ_NO_RESUBMIT}
fi
```

If the current cycle number has reached the limit, the script signals to qq that the job should not be resubmitted.

> [!NOTE]
> `QQ_NO_RESUBMIT` is an environment variable set by qq that holds a special exit code. Exiting with it means "this job finished successfully, but do not submit another cycle". Read [this section of the manual](job_types/loop_job.md#forcing-qq-not-to-resubmit) for details.

Now we have a job that runs for a fixed number of cycles. The condition could be something else entirely; we could, for instance, inspect a property in the output state file and stop once it converges. The cycle count is enough for this example.

With cycle tracking in place, we can also make the output files unique by putting the cycle number into their names. That way each cycle writes its own files, nothing gets overwritten, and we stop losing data.

```bash
#!/usr/bin/env -S qq run

# qq job-type continuous

N_CYCLES=10

# if the cycle file exists, increment the cycle number
if [ -f cycle ]; then
    CYCLE=$(($(cat cycle) + 1))
# otherwise start from 1
else
    CYCLE=1
fi
echo ${CYCLE} > cycle

# if there is a state file from the previous cycle,
# use it to continue the calculation
if [ -f output_$((CYCLE - 1)).state ]; then
    sick -i output_$((CYCLE - 1)).state \
        -s settings.toml \
        -r results_${CYCLE}.dat \
        -o output_${CYCLE}.state
# if there is no state file, start from scratch
else
    sick -s settings.toml -r results_${CYCLE}.dat -o output_${CYCLE}.state
fi

# if the cycle limit is reached, stop the job
if [ ${CYCLE} -ge ${N_CYCLES} ]; then
    echo "Cycle limit reached. Stopping."
    exit ${QQ_NO_RESUBMIT}
fi
```

The results file is now named `results_${CYCLE}.dat`, so `results_1.dat`, `results_2.dat`, and so on, and the state file is named `output_${CYCLE}.state`. Every cycle has its own output files. Notice that we no longer rename anything: instead of changing `output.state` to `input.state`, we simply read the state file of the previous cycle, whose name we can construct from the cycle number.

## Creating a storage directory

The script works, but if the calculation runs for many cycles, the output files start to pile up in the job directory. That matters more than it might seem, because by default qq copies everything in the job directory to the working directory on the compute node, where the job actually runs (see [this section of the manual](job_types/standard_job.md#2-preparing-the-working-directory) if this is new for you). With large output files we would be copying a lot of data back and forth every cycle, which slows the job down. On some clusters the working directory also has a limited capacity that you can exceed this way.

To avoid this, we can put the finished files into a separate directory and tell qq not to copy that directory to the compute node.

```bash
#!/usr/bin/env -S qq run

# qq job-type continuous
# do not copy the `storage` directory to the working directory
# qq exclude storage

N_CYCLES=10

# create the storage directory
mkdir -p storage

# if the cycle file exists, increment the cycle number
if [ -f cycle ]; then
    CYCLE=$(($(cat cycle) + 1))
# otherwise start from 1
else
    CYCLE=1
fi
echo ${CYCLE} > cycle

# if there is a state file from the previous cycle,
# use it to continue the calculation
if [ -f input.state ]; then
    sick -i input.state \
        -s settings.toml \
        -r storage/results_${CYCLE}.dat \
        -o output.state
# if there is no state file, start from scratch
else
    sick -s settings.toml -r storage/results_${CYCLE}.dat -o output.state
fi

# archive the state file of this cycle
cp output.state storage/output_${CYCLE}.state

# and hand it over to the next cycle
mv output.state input.state

# if the cycle limit is reached, stop the job
if [ ${CYCLE} -ge ${N_CYCLES} ]; then
    echo "Cycle limit reached. Stopping."
    exit ${QQ_NO_RESUBMIT}
fi
```

The results files go straight into `storage`. Because the directory is excluded, the copy of it in the working directory starts out empty every cycle, so we never drag the results of previous cycles onto the compute node; when the job finishes, what this cycle wrote there is copied back and joins the files already in `storage` in the job directory. The state files are archived the same way, one per cycle, under a name that says which cycle produced them.

Notice that the file carrying the state into the next cycle keeps the fixed name `input.state`, and that we archive a _copy_ of it. That is because `storage` is excluded, so nothing inside it is present in the working directory when the job runs; the script cannot read from its own archive (at least not easily). The one file the next cycle needs must stay in the job directory, and since its name never changes, it is simply overwritten each cycle and nothing accumulates.

> [!TIP]
> `qq exclude` only affects what is copied _into_ the working directory. Files that your job writes are still copied back to the job directory when the job succeeds. So an excluded directory is a good place for anything you want to keep but will never need to read again during the run.

This works, but look at what we still do not have. The [qq runtime files](runtime_files.md), which include the logs from `sick`, are overwritten every cycle, and there is no simple way to archive them by hand. Every cycle of the job has the same name in the batch system, so we have to open the `cycle` file to find out which cycle we are on. We are copying the state file twice and juggling two names for it, because we cannot easily read anything back out of the archive. And compared to the script we started with, this is a looot of boilerplate. There is a better way, and it is called a loop job.

## A proper loop job

Loop jobs are continuous jobs that do cycle tracking and archiving for you. They automate everything we implemented by hand in the previous sections.

Done properly, the whole script collapses to this:

```bash
#!/usr/bin/env -S qq run

# qq job-type loop
# qq loop-end 10
# qq archive-format job%04d
# qq archive storage

# create strings for naming files in the current and the next cycle
printf -v CURR "${QQ_ARCHIVE_FORMAT}" "${QQ_LOOP_CURRENT}"
printf -v NEXT "${QQ_ARCHIVE_FORMAT}" "$((QQ_LOOP_CURRENT + 1))"

# if there is a state file for the current cycle,
# use it to continue the calculation
if [ -f "${CURR}.state" ]; then
    sick -i "${CURR}.state" -s settings.toml -r "${CURR}.dat" -o "${NEXT}.state"
# if there is no state file, start from scratch
else
    sick -s settings.toml -r "${CURR}.dat" -o "${NEXT}.state"
fi
```

This may look a bit like magic. How does qq know which files to archive? Where do the archiving operations even happen?

Let's go through it step by step.

#### qq directives

```bash
# qq job-type loop
# qq loop-end 10
# qq archive-format job%04d
# qq archive storage
```

These are [qq directives](commands/qq_submit.md#specifying-options-in-the-script), that is, submission options. You already know `job-type`. `loop-end` says which cycle is the last one. `archive-format` sets the naming convention for archived files. `archive` says where the archived files go.

What does "naming convention for archived files" mean? With `job%04d`, any file or directory whose name contains `job` followed by a four-digit number (with leading zeros) is treated as a file to archive and moved into the archive at the end of the cycle, before the next cycle is submitted. The archive is the `storage` directory inside the job directory. qq creates it for you and automatically excludes it from being copied to the working directory, so there is no needless copying and no `qq exclude` directive needed.

> [!NOTE]
> The format string is an ordinary `printf` format. `job%04d` produces `job0001`, `job0002`, and so on, so a results file named `job0007.dat` belongs to cycle 7. Pick a width that comfortably covers the number of cycles you plan to run.

#### Staging strings

```bash
printf -v CURR "${QQ_ARCHIVE_FORMAT}" "${QQ_LOOP_CURRENT}"
printf -v NEXT "${QQ_ARCHIVE_FORMAT}" "$((QQ_LOOP_CURRENT + 1))"
```

Here we build two helper strings, `CURR` and `NEXT`, used to name the files that will be archived. `QQ_ARCHIVE_FORMAT` holds the format from the `archive-format` directive and `QQ_LOOP_CURRENT` holds the number of the current cycle; both are set by qq and available in every loop job. In the first cycle, `CURR` is `job0001` and `NEXT` is `job0002`.

But why do we need `NEXT` at all? The next section answers that.

#### Running the calculation

```bash
if [ -f "${CURR}.state" ]; then
    sick -i "${CURR}.state" -s settings.toml -r "${CURR}.dat" -o "${NEXT}.state"
# if there is no state file, start from scratch
else
    sick -s settings.toml -r "${CURR}.dat" -o "${NEXT}.state"
fi
```

If the state file for the current cycle exists, we use it as input; otherwise we start from scratch.

Wait. Isn't `${CURR}.state` in the archive? How can we read it as if it were sitting in the working directory of the job?

That is the other half of the archiving magic. At the start of every cycle, qq copies the archived files **belonging to the current cycle** into the working directory. "Belonging to the current cycle" means that the name of the file or directory contains `job` followed by the current cycle number, formatted as a four-digit number with leading zeros. So in cycle 7, qq pulls `job0007.state` out of the archive for you, and at the end of the cycle it moves everything matching the format (for any cycle) back in.

This also explains the output state file being named `${NEXT}.state`. We will need it as the input of the next cycle, so we label it with the next cycle number, and qq will bring it back in when that cycle starts.

> [!TIP]
> A simple rule of thumb: name anything the current cycle _consumes_ with `${CURR}` and anything the next cycle _will consume_ with `${NEXT}`. Files that nothing else will read, such as results/trajectories/logs, should use `${CURR}` as well; they will be archived and stay there. Anything that is not archived, stays in the working directory and is copied back to the input directory of the job.

And those are all the parts of the script.

#### Summary

To put the whole thing in order: you submit a new loop job and it is assigned cycle number 1. Once it starts running, it creates the working directory on the compute node and copies the job directory into it, skipping the archive. It then pulls out of the archive every file whose name matches the archive format with the current cycle number, so that the script finds those files as if they had been there with it all along.

Your script runs, reads the files belonging to the current cycle, and writes new ones, naming anything needed later after the cycle that will consume it. When the script exits, qq moves all files matching the archive format into the archive, copies everything else back to the job directory, and submits the next cycle.

The next cycle is assigned number 2. It archives the runtime files of the previous cycle, creates its own working directory, copies the files there, runs the script, and so on. This execution and resubmission loop continues until the cycle limit is reached or until your script exits with `QQ_NO_RESUBMIT`. Then the job simply ends.

> [!NOTE]
> Runtime files are archived by the _following_ cycle, not by the cycle that produced them. That is why the runtime files of the last cycle stay in the job directory: there is no further cycle to move them.

> [!TIP]
> If this still does not connect and you are a visual person, [this diagram](job_types/loop_job.md#data-flow-in-a-loop-job-cycle) might help.

---

With the knowledge gained from this tutorial, you should hopefully be able to write your own loop job scripts and understand what qq does with them behind the scenes.

# Submitting non-bash scripts

When you submit a qq job using [`qq submit`](commands/qq_submit.md), the submitted script is executed using a special [`qq run`](commands/qq_run.md) interpreter. This interpreter does not just run the script, but also performs many operations related to job preparation: for example, it creates the working directory for the job, transfers files, and, in the case of loop jobs, also archives files and resubmits jobs. To execute the actual commands in the submitted script, `qq run` uses the standard Linux `bash` shell by default. That's why you typically write your script in bash with a [qq run shebang](commands/qq_run.md) and potentially some [qq directives](commands/qq_submit.md#specifying-options-in-the-script).

However, with qq you can also specify a different interpreter than bash to execute the commands in your submitted script. This means you don't need to write a wrapper around e.g. your Python script — you can submit the script itself and tell qq: "Run it using Python".

To control what interpreter `qq run` uses to execute the submitted script, specify the `--interpreter` option of [`qq submit`](commands/qq_submit.md). For example, to submit a Python script, you can run:

```bash
qq submit my_script.py (...) --interpreter python
```

The script `my_script.py` will be executed using `python`. **You need to make sure that Python is available on the compute node where your job is to be executed, otherwise your job will fail.** Also make sure that the `python` executable on the compute node starts the Python interpreter of the expected version with the expected packages your script requires.

> Note that no matter what interpreter you want your script to be run with, you must **always include the standard qq run shebang**: `#!/usr/bin/env -S qq run`. You can easily add it to your script using [`qq shebang`](commands/qq_shebang.md).

## Submitting a simple Python script

Let's look at a more complete and concrete example. Suppose we have a simple Python script estimating the value of π using a Monte Carlo simulation.
```python
#!/usr/bin/env -S qq run

# qq interpreter python

"""Estimate the value of pi using the Monte Carlo method."""

import random

N_SAMPLES = 1_000_000

def estimate_pi(n_samples: int) -> float:
    inside = 0
    for _ in range(n_samples):
        x = random.uniform(-1, 1)
        y = random.uniform(-1, 1)
        if x**2 + y**2 <= 1:
            inside += 1
    return 4 * inside / n_samples

def main():
    print(f"Estimating pi using {N_SAMPLES:,} samples...")
    result = estimate_pi(N_SAMPLES)
    print(f"Estimated pi: {result:.6f}")
    print(f"Actual pi:    {3.141593:.6f}")
    print(f"Error:        {abs(result - 3.141593):.6f}")

if __name__ == "__main__":
    main()
```

We save the script into a file `calc_pi.py` and submit it to the batch system:

```bash
qq submit -q default --ncpus 1 calc_pi.py
```

We do not need to specify the Python interpreter on the command line, as it is already specified in the body of the script using the qq directive `# qq interpreter python`. Upon submission and job start, everything happens [as usual](job_types/standard_job.md) — including the creation of the working directory — but the script is interpreted using Python. Once the script finishes, the clean-up happens as for other qq jobs. The result of the calculation will be stored in `calc_pi.out` in the input (submission) directory once the job finishes.

> Here we are using the Python executable name (just `python`), which is automatically expanded using the [`which`](https://en.wikipedia.org/wiki/Which_(command)) command to the full path of the interpreter on the compute node (e.g., `/usr/bin/python`). If you do not trust this automatic expansion, you can always specify the full path to the interpreter yourself (e.g., `# qq interpreter /usr/bin/python` or `# qq interpreter /path/to/my/own/python/on/shared/storage`).

## Submitting a looping Python script

With qq, you can run [loop jobs](job_types/loop_job.md) even when using a non-bash interpreter. Loop jobs are useful when your script takes a very long time to finish and you have a mechanism to restart from checkpoints.

```python
#!/usr/bin/env -S qq run

# Example qq loop job script written in Python.
#
# This script performs a fake iterative calculation across multiple cycles,
# demonstrating how to use qq python loop jobs with checkpointing. Each cycle loads
# the running state from a checkpoint file written by the previous cycle,
# performs a fixed number of iterations that increment a running total, writes
# the results for the current cycle, and writes a checkpoint for the next one.
# On the first cycle, the state is initialized from scratch.

# qq interpreter python
# qq job-type loop
# qq loop-end 10
# qq archive storage
# qq archive-format job%04d

import os
import json

########################################
#         Calculation options          #
########################################

# number of iterations of the fake calculation per cycle
ITERATIONS_PER_CYCLE = 1000

# increment added to the running total in each iteration
INCREMENT = 0.01

########################################
#          Execution section           #
########################################

# read qq environment variables
loop_current = int(os.environ["QQ_LOOP_CURRENT"])
loop_start = int(os.environ["QQ_LOOP_START"])
archive_format = os.environ["QQ_ARCHIVE_FORMAT"]

# format the current and next cycle file prefixes
curr  = archive_format % loop_current
next_ = archive_format % (loop_current + 1)

print(f"Starting cycle {loop_current}.")

# load state from checkpoint if this is not the first cycle
if loop_current == loop_start:
    print(f"First cycle - initializing state.")
    total = 0.0
    iteration = 0
else:
    checkpoint_file = f"{curr}.json"
    print(f"Loading checkpoint from '{checkpoint_file}'.")
    with open(checkpoint_file) as f:
        state = json.load(f)
    total = state["total"]
    iteration = state["iteration"]
    print(f"Resuming from iteration {iteration}, total = {total:.4f}.")

# perform some fake calculation
print(f"Running {ITERATIONS_PER_CYCLE} iterations...")
for i in range(ITERATIONS_PER_CYCLE):
    total += INCREMENT
    iteration += 1

print(f"Cycle {loop_current} done. Iteration = {iteration}, total = {total:.4f}.")

# write the results for this cycle
results_file = f"{curr}.txt"
print(f"Writing results to '{results_file}'.")
with open(results_file, "w") as f:
    f.write(f"Cycle:     {loop_current}\n")
    f.write(f"Iteration: {iteration}\n")
    f.write(f"Total:     {total:.6f}\n")

# write the checkpoint for the next cycle
# this file must be written so that qq can determine the next cycle number
checkpoint_file = f"{next_}.json"
print(f"Writing checkpoint to '{checkpoint_file}'.")
with open(checkpoint_file, "w") as f:
    json.dump({"total": total, "iteration": iteration}, f)

print(f"Cycle {loop_current} finished successfully.")
```

We save the script into a file `loop_job.py` and submit it to the batch system:
```bash
qq submit -q default --ncpus 1 loop_job.py
```

The job is a regular qq [loop job](job_types/loop_job.md) with the script interpreted using Python. Once a cycle finishes successfully, the next one is automatically submitted until the job reaches cycle number 10 (`# qq loop-end 10`). Files are archived according to [standard qq rules for file archiving](job_types/loop_job.md#working-with-the-archive).

***

> **Important note:** If the language you are writing the script in does not interpret lines starting with `#` as comments (e.g., Octave, Lua), you cannot use [qq directives](commands/qq_submit.md#specifying-options-in-the-script), including the `# qq interpreter` directive. In that case, you can still — and in fact *must* — specify all submission options on the command line when submitting the script.
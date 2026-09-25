# Using qq in Python

> [!TIP]
> Looking for how to run Python scripts as qq jobs instead? See [this section of the manual](interpreters.md).

qq is built on top of `qq_lib`, a Python library that exposes core qq functionality programmatically. You can install `qq_lib` to integrate qq workflows directly into your Python scripts.

The recommended way to use `qq_lib` is to use the [uv package manager](https://docs.astral.sh/uv/getting-started/installation/).

To add `qq_lib` to your project:

```bash
uv add git+https://github.com/VachaLab/qq.git --tag v0.13.0
```

Alternatively, you can add it directly to a specific script:

```bash
uv add git+https://github.com/VachaLab/qq.git --tag v0.13.0 --script [YOUR_SCRIPT].py
```

> [!NOTE]
> Be aware that `qq_lib` requires python 3.13 or higher.

Then import qq classes and utilities in your Python code:

```python
from qq_lib.info import Informer
from qq_lib.kill import Killer
```

And use them:

```python
informer = Informer.from_file("my_job.qqinfo")
print(informer.get_real_state())

killer = Killer.from_informer(informer)
killer.kill()
```

See the [Python API documentation](https://qq.readthedocs.io/en/stable/qq_lib.html) for details on available modules, classes, and functions.

## Official qq scripts

qq offers several official helper scripts built on top of `qq_lib`. These tools automate common workflows but are not part of core qq functionality. You can find them in the [qq GitHub repository](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts).

To use them, the recommended approach is to use the [uv package manager](https://docs.astral.sh/uv/getting-started/installation/). If you have `uv` installed, download the script, make it executable (`chmod u+x SCRIPT`), and run it (`./SCRIPT`). If you use the scripts frequently, consider adding their directory to your `PATH`.

> [!NOTE]
> The official qq scripts serve several purposes. One purpose is testing new features before they are added to qq itself. Some of the scripts may therefore eventually become part of core qq in some form or other, as happened with [job collections](job_collections.md). Another purpose is extending qq for specific applications, such as `gmx-eta` for Gromacs. These scripts will never become part of core qq, since qq aims to be as general as possible and not tied to any particular software. A third purpose is showing how to use `qq_lib` in your own Python scripts.

> [!CAUTION]
> qq scripts are largely untested, completely unstable, and unversioned. Their functionality can change at any time without any warning or notice.

## [gmx-eta](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts/gmx-eta)

`gmx-eta` estimates the remaining runtime of a Gromacs simulation. Run it in a directory containing a qq job, supply job ID(s), or use the `--all` flag.

> [!IMPORTANT]
> `gmx-eta` requires that your Gromacs `mdrun` command is executed with the `-v` flag.

### Usage

```bash
usage: gmx-eta [-h] [--all] [job_id ...]

Get the estimated time of a Gromacs simulation finishing.

positional arguments:
  job_id      Job ID(s). Optional. If not provided, ETA is obtained for the newest job submitted from the current directory.

options:
  -h, --help  show this help message and exit
  --all, -a   Show ETA for all jobs.
```

### Examples

Using a single job ID:

```bash
$ gmx-eta 12345
[12345] gromacs_job: Simulation will finish in 06:41:07.
```

Using multiple job IDs:

```bash
$ gmx-eta 12345 12356
[12345] gromacs_job: Simulation will finish in 06:41:07.
[12356] gromacs_job: Simulation will finish in 05:23:57.
```

Using the `--all` flag:

```bash
$ gmx-eta --all
[12345] gromacs_job: Simulation will finish in 06:41:07.
[12356] gromacs_job: Simulation will finish in 05:23:57.
[12444] gromacs_job_new: Simulation will finish in 08:45:12.
[12458] gromacs_job_new: Simulation will finish in 11:33:01.
```

Without arguments inside an input directory of a job:

```bash
$ gmx-eta
[12444] gromacs_job_new: Simulation will finish in 08:45:12.
```

---

## [loop-eta](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts/loop-eta)

`loop-eta` estimates when a qq loop job will finish. Run it in a directory containing a loop job, or supply one or more directories.

For each job, `loop-eta` shows the current cycle, the median queue wait and run time per cycle, the remaining time, and the estimated completion time. The estimate assumes that each remaining cycle waits and runs for the median time. Cycles that ran much longer than the median are listed separately, together with the node they ran on.

> [!IMPORTANT]
> `loop-eta` can only estimate the completion time after at least one cycle of the loop job has finished successfully.
>
> `loop-eta` is not usable if your script can end the loop job early using [`QQ_NO_RESUBMIT`](job_types/loop_job.md#forcing-qq-not-to-resubmit). The estimate assumes that the job runs through all cycles up to the last one.

### Usage

```bash
Usage: loop-eta [OPTIONS] [DIRECTORIES]...

  Show timing statistics and estimated completion times of qq loop jobs in the
  specified directories. If no directory is specified, the current directory
  is used.

Options:
  --last INTEGER RANGE       Use only the last N finished cycles of each job.
                             By default, all finished cycles are used.  [x>=1]
  --slow-factor FLOAT RANGE  Report finished cycles whose run time exceeds the
                             median run time by this factor.  [default: 1.5;
                             x>=1.0]
  -h, --help                 Show this message and exit.
```

---

## [resource-usage](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts/resource-usage)

`resource-usage` calculates the computational resources used by a collection of qq jobs and writes them into a CSV file. Run it in a directory containing qq jobs, or supply one or more directories.

The CSV file contains one row per directory and a final row with the totals. For each directory, it lists the number of jobs, the total run time, and the CPU-hours, GPU-hours, and node-hours used. For loop jobs, all cycles stored in the archive are included.

> [!IMPORTANT]
> `resource-usage` only counts jobs that have completed. Queued and running jobs are ignored. Failed attempts of respawned jobs are not counted, since respawning removes their records. For continuous jobs, only the last cycle is counted. The time spent copying input files to the working directory is not included, so the numbers are slightly lower than those reported by the batch system.

### Usage

```bash
Usage: resource-usage [OPTIONS] [DIRECTORIES]...

  Write the resources used by qq jobs in the specified directories into a CSV
  file. If no directory is specified, the current directory is used.

Options:
  -o, --output FILE  Path to the output CSV file. An existing file is
                     overwritten.  [default: resources.csv]
  -h, --help         Show this message and exit.
```

---

## [low-cpu-check](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts/low-cpu-check)

`low-cpu-check` reports your running qq jobs with low CPU utilization. It is meant to be run periodically as a cron job. It prints nothing unless it finds such a job, so cron only sends you an e-mail when something needs your attention.

A job is only checked after it has been running for a grace period (20 minutes by default), since CPU utilization takes some time to rise after a job starts. Each job is reported at most once every 6 hours by default. The times of the last reports are stored in `~/.local/state/qq/low_cpu_check.json`.

> [!IMPORTANT]
> `low-cpu-check` only works on PBS, because Slurm does not report CPU utilization.

### Setting up the cron job

Open your crontab using `crontab -e` and add the following lines:

```bash
SHELL=/bin/bash
BASH_ENV=$HOME/.bashrc

0 * * * * /path/to/low-cpu-check -s meta
```

This runs the check at the start of every hour for jobs on the Metacentrum Grid. Use `-s` several times to check jobs on more servers (e.g., `-s robox -s sokar -s meta`).

Cron runs commands with a minimal environment that usually does not include `uv` or the batch system commands. Setting `SHELL` and `BASH_ENV` makes cron load your `.bashrc` before running the script. If your `.bashrc` exits early for non-interactive shells (for example with `[[ $- != *i* ]] && return`), make sure your `PATH` is set before that line.

### Usage

```bash
Usage: low-cpu-check [OPTIONS]

  Report running qq jobs with low CPU utilization. Prints nothing if all jobs
  are fine. Only works on PBS.

Options:
  -s, --server TEXT               Batch server to collect jobs from. Can be
                                  specified multiple times. Shortcuts such as
                                  'meta' are supported. If not specified, the
                                  current server is used.
  -t, --threshold INTEGER RANGE   CPU utilization in percent below which a job
                                  is reported.  [default: 50; 1<=x<=100]
  -g, --grace INTEGER RANGE       Minutes after the start of a job during
                                  which its CPU utilization is not checked.
                                  [default: 20; x>=0]
  -r, --repeat-after INTEGER RANGE
                                  Hours before a job that still has low CPU
                                  utilization is reported again.  [default: 6;
                                  x>=0]
  --state-file FILE               File storing when each job was last
                                  reported.  [default: /home/user/.local/state
                                  /qq/low_cpu_check.json]
  -h, --help                      Show this message and exit.
```

# Using qq in Python

qq is built on top of `qq_lib`, a Python library that exposes core qq functionality programmatically. You can install `qq_lib` to integrate qq workflows directly into your Python scripts.

The recommended way to use `qq_lib` is to use the [uv package manager](https://docs.astral.sh/uv/getting-started/installation/).

To add `qq_lib` to your project:

```bash
uv add git+https://github.com/VachaLab/qq.git --tag v0.7.1
```

Alternatively, you can add it directly to a specific script:

```bash
uv add git+https://github.com/VachaLab/qq.git --tag v0.7.1 --script [YOUR_SCRIPT].py
```

Then import qq classes and utilities in your Python code:

```python
from qq_lib.info import Informer
from qq_lib.kill import Killer
```

And use them:

```python
informer = Informer.fromFile("my_job.qqinfo")
print(informer.getRealState())

killer = Killer.fromInformer(informer)
killer.kill()
```

See the [Python API documentation](https://qq.readthedocs.io/) for details on available modules, classes, and functions.

## Official qq scripts

qq offers several official helper scripts built on top of `qq_lib`. These tools automate common workflows—especially useful when running many Gromacs simulations—but are not part of core qq functionality. You can find them in the [qq GitHub repository](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts).

Again, the recommended approach is to use the [uv package manager](https://docs.astral.sh/uv/getting-started/installation/). If you have `uv` installed, download the script, make it executable (`chmod u+x SCRIPT`), and run it (`./SCRIPT`). If you use the scripts frequently, consider adding their directory to your `PATH`.

## [gmx-eta](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts/gmx-eta)

`gmx-eta` estimates the remaining runtime of a Gromacs simulation. Run it in a directory containing a qq job, or supply a specific job ID.

### Usage
```bash
usage: gmx-eta [-h] [job_id]

Get the estimated time of a Gromacs simulation finishing.

positional arguments:
  job_id                Job ID. Optional.

options:
  -h, --help            show this help message and exit
```

### Example
```bash
$ gmx-eta 12345
Simulation will finish in 06:41:07.
```

> **Note:** `gmx-eta` requires that your Gromacs `gmx mdrun` command is executed with the `-v` flag.

---

## [multi-check](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts/multi-check)

`multi-check` scans multiple directories for qq jobs and reports their collective status. It uses multithreading to significantly speed up job-state inspection compared to checking jobs individually.

### Usage
```bash
usage: multi-check [-h] [-t THREADS] [--fix] directories [directories ...]

Check the state of qq jobs in multiple directories.

positional arguments:
  directories           Directories containing qq info files.

options:
  -h, --help            show this help message and exit
  -t, --threads THREADS Number of worker threads (default: 16)
  --fix                 Resubmit all failed and killed jobs.
```

### Example check
```bash
$ multi-check win??

Collecting job states ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00

FAILED          4
win05 win06 win07 win08

FINISHED        3
win45 win48 win49

QUEUED          35
win01 win02 win03 win04 win09 win10 win11 win12 win13 win14 win15 win16 win17 win18 win20 win21 win22 win23 win24 win25 win26 win27 win28 win29 win30 win31 win32 win33 win34 win35 win36 win38 win39 win42 win43

RUNNING         9
win19 win37 win40 win41 win44 win46 win47 win50 win51

TOTAL           51
```

You may also use `--fix` to automatically attempt to fix and resubmit jobs in **FAILED** or **KILLED** states. Jobs are submitted with the same parameters originally used.

### Example fix
```bash
$ multi-check win?? --fix

Collecting job states ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00

FAILED          4
win05 win06 win07 win08

FINISHED        3
win45 win48 win49

QUEUED          35
win01 win02 win03 win04 win09 win10 win11 win12 win13 win14 win15 win16 win17 win18 win20 win21 win22 win23 win24 win25 win26 win27 win28 win29 win30 win31 win32 win33 win34 win35 win36 win38 win39 win42 win43

RUNNING         9
win19 win37 win40 win41 win44 win46 win47 win50 win51

TOTAL           51

***********************************

Fixing jobs ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00

FIXED SUCCESSFULLY        4
win05 win06 win07 win08

COULD NOT FIX             0
```

---

## [multi-submit](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts/multi-submit)

`multi-submit` submits qq jobs from multiple directories in bulk. All jobs must use the same submission script name and request identical resources. The resource specification from the *first* submitted job is applied to all others.

### Usage
```bash
usage: multi-submit [-h] script directories [directories ...]

Submit qq jobs from multiple directories. All jobs must request the same resources!

positional arguments:
  script                Name of the script to submit.
  directories           Directories containing qq info files.

options:
  -h, --help            show this help message and exit
```

### Example
```bash
$ multi-submit qq_loop_md win?? -q default --ncpus=8 --walltime=12h

Submitting jobs ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00

SUBMITTED SUCCESSFULLY    51
win01 win02 win03 win04 win05 win06 win07 win08 win09 win10 win11 win12 win13 win14 win15 win16 win17 win18 win19 win20 win21 win22 win23 win24 win25 win26 win27 win28 win29 win30 win31 win32 win33 win34 win35 win36 win37 win38 win39 win40 win41 win42 win43 win44 win45 win46 win47 win48 win49 win50 win51

COULD NOT SUBMIT          0
```

---

## [multi-kill](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts/multi-kill)

`multi-kill` terminates qq jobs across multiple directories in parallel. Because it uses multithreading, it is significantly faster than running `qq kill` for each job independently.

### Usage
```bash
usage: multi-kill [-h] [-t THREADS] directories [directories ...]

Kill qq jobs in multiple directories.

positional arguments:
  directories           Directories containing qq info files.

options:
  -h, --help            show this help message and exit
  -t, --threads THREADS
                        Number of worker threads (default: 16)
```

### Example
```bash
$ multi-kill win??
Killing jobs ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00

KILLED SUCCESSFULLY       51
win01 win02 win03 win04 win05 win06 win07 win08 win09 win10 win11 win12 win13 win14 win15 win16 win17 win18 win19 win20 win21 win22 win23 win24 win25 win26 win27 win28 win29 win30 win31 win32 win33 win34 win35 win36 win37 win38 win39 win40 win41 win42 win43 win44 win45 win46 win47 win48 win49 win50 win51

COULD NOT KILL            0
```
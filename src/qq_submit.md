# qq submit

The `qq submit` command is used to submit qq jobs to the batch system. It is qq's equivalent of Infinity's `psubmit`.

***

> **Quick comparison with psubmit**
> - `qq submit` does not ask for confirmation; it behaves like `psubmit (...) -y`.
> - Options and parameters are specified differently. The only positional argument is the script name — everything else is an option.
>   You can see all supported options using `qq submit --help`.
>
>   **Infinity:**
>   ```bash
>   psubmit default run_script ncpus=8,walltime=12h,props=cl_zero -y
>   ```
>
>   **qq:**
>   ```bash
>   qq submit -q default run_script --ncpus=8 --walltime=12h --props=cl_zero
>   ```
>
> - Options can also be specified directly in the submitted script, or as a mix of in-script and command-line definitions. Command-line options always take precedence.

***

### Description

Submits a qq job to the batch system.

```bash
qq submit [OPTIONS] SCRIPT
```

**SCRIPT** — Path to the script to submit.

The submitted script must contain the [`qq run` shebang](qq_run.md).

When the job is successfully submitted, `qq submit` creates a `.qqinfo` file for tracking the job’s state.

#### Options

There are many available options. For a full list, run `qq submit --help`.

All options can also be specified directly inside the submitted script (see below).

### Specifying options in the script

Instead of specifying submission options on the command line, you can include them directly in the script using *qq directives*.

qq directives follow this format: `# qq <option>=<value>` or `# qq <option> <value>` (both are equivalent).

The word `qq` is case-insensitive (`qq`, `QQ`, `Qq`, and `qQ` are all valid), and spacing is flexible.
All qq directives must appear at the beginning of the script, before any executable commands.

**Example:**

```bash
#!/usr/bin/env -S qq run

# qq job-type loop
# qq loop-end 10
# qq archive storage
# qq archive-format md%04d

# qq ncpus=8
# qq ngpus=1
# qq walltime=1d

metamodule add ...
```

> In the example above, kebab-case is used for option names, but qq directives also support snake_case, camelCase, and PascalCase.  
> For example: `# qq job-type loop`, `# qq JobType loop`, `# qq jobType loop`, and `# qq job_type loop` are all equivalent.

Command-line options always take precedence over options defined in the script body.

### Examples

```bash
qq submit run_script.sh -q default --ncpus=8 --workdir=scratch_local --worksize-per-cpu=2gb --walltime=2d --props=multithreading
```

Submits the script `run_script.sh` to the `default` queue, requesting 8 CPU cores and 16 GB of local scratch space (2 GB per core). The requested walltime is 48 hours, and the job must run on a node with the `multithreading` property. Additional options may come from the script or queue defaults, but command-line options take precedence.

```bash
qq submit run_script.sh
```

Submits the script `run_script.sh`, taking all submission options from the script itself or from queue/server defaults.
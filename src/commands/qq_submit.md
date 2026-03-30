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
>   psubmit cpu run_script ncpus=8,walltime=12h,props=cl_zero -y
>   ```
>
>   **qq:**
>   ```bash
>   qq submit -q cpu run_script --ncpus 8 --walltime 12h --props cl_zero
>   ```
>
> - Options can also be specified directly [in the submitted script](#specifying-options-in-the-script), or as a mix of in-script and command-line definitions. Command-line options always take precedence (including list options since qq v0.8).
> - Unlike with `psubmit`, you do **not** have to execute `qq submit` directly from the directory with the submitted script. You can run `qq submit` from anywhere and provide the path to your script. The job's input directory will always be the submitted script's parent directory.
> - `qq submit` has better support for multi-node jobs than `psubmit` as it allows specifying resource requirements per requested node.

***

### Description

Submits a qq job to the batch system.
```bash
qq submit [OPTIONS] SCRIPT
```

**SCRIPT** — Path to the script to submit.

The submitted script must contain the [qq run shebang](qq_run.md). You can add it to your script by running [`qq shebang SCRIPT`](qq_shebang.md).

When the job is successfully submitted, `qq submit` creates a `.qqinfo` file for tracking the job's state.

#### Options

##### General settings

`-q`, `--queue` `TEXT` — Name of the queue to submit the job to.

`-s`, `--server` `TEXT` — Name of the batch server to submit the job to. If not specified, the job is submitted to the default batch server. Only supported on Metacentrum-family clusters. Read more about specifying a server [here](../servers.md).

`--account` `TEXT` — Account to use for the job. Required only in environments with accounting (e.g., IT4Innovations).

`--job-type` `TEXT` — Type of the job. Defaults to **standard**. Available types: 'standard', 'loop', 'continuous'. Read more about job types [here](../job_types/job_types.md).

`--exclude` `TEXT` — Colon-, comma-, or space-separated list of files or directories that should **not** be copied to the working directory. Paths must be relative to the input directory.

`--include` `TEXT` — Colon-, comma-, or space-separated list of files or directories to copy into the working directory in addition to the input directory contents. These files are not copied back after job completion. Paths must be absolute or relative to the input directory. Ignored if the input directory is used as the working directory.

`--depend` `TEXT` — Comma- or space-separated list of job dependencies in the format '<type>=<job_id>[:<job_id>...]'. Available types: **after** (after start), **afterok** (after success), **afternotok** (after failure/kill), **afterany** (after completion regardless of outcome). Multiple job IDs in one expression (colon-separated) require all listed jobs to satisfy the condition. Multiple expressions must all be satisfied before the job starts. Examples: 'afterok=1234', 'after=456:789', 'afterok=123,afternotok=678'. Read more about dependencies [here](../dependencies.md).

`--transfer-mode` `TEXT` — Colon-, comma-, or space-separated list of transfer modes controlling when working directory files are transferred to the input directory. Modes: **success** (exit code 0), **failure** (non-zero exit code), **always**, **never**, or a specific exit code number (e.g., **42**). Combine modes; files transfer if any apply. Defaults to **success**. On transfer, the working directory is deleted; otherwise it is preserved. Killed jobs are never transferred automatically. Ignored if the input directory is used as the working directory. Examples: 'success', 'always', 'success:42', '1 2 3'.

`--interpreter` `TEXT` — Executable name or absolute path of the interpreter used to run the submitted script. Defaults to **bash**. The interpreter must be available on the computing node. Read more about specifying interpreters [here](../interpreters.md).

`--batch-system` `TEXT` — Name of the batch system used to submit the job. If not specified, the value of the environment variable 'QQ_BATCH_SYSTEM' is used or the system is auto-detected.

##### Requested resources
Memory and storage sizes are specified as 'N<unit>' where unit is one of b, kb, mb, gb, tb, pb (e.g., 500mb, 32gb).

**Job resources are described in more detail in the [Job resources](../resources/resources.md) section.**

`--nnodes` `INTEGER` — Number of nodes to allocate for the job.

`--ncpus-per-node` `INTEGER` — Number of CPU cores to allocate per node.

`--ncpus` `INTEGER` — Total number of CPU cores to allocate for the job. Overrides **--ncpus-per-node**.

`--mem-per-cpu` `TEXT` — Memory to allocate per CPU core.

`--mem-per-node` `TEXT` — Memory to allocate per node. Overrides **--mem-per-cpu**.

`--mem` `TEXT` — Total memory to allocate for the job. Overrides **--mem-per-cpu** and **--mem-per-node**.

`--ngpus-per-node` `INTEGER` — Number of GPUs to allocate per node.

`--ngpus` `INTEGER` — Total number of GPUs to allocate for the job. Overrides **--ngpus-per-node**.

`--walltime` `TEXT` — Maximum runtime for the job. Examples: '1d', '12h', '10m', '24:00:00'.

`--work-dir`, `--workdir` `TEXT` — Type of working directory to use for the job. Available types [depend on the environment](../resources/work_dir.md).

`--work-size-per-cpu`, `--worksize-per-cpu` `TEXT` — Storage to allocate per CPU core.

`--work-size-per-node`, `--worksize-per-node` `TEXT` — Storage to allocate per node. Overrides **--work-size-per-cpu**.

`--work-size`, `--worksize` `TEXT` — Total storage to allocate for the job. Overrides **--work-size-per-cpu** and **--work-size-per-node**.

`--props` `TEXT` — Colon-, comma-, or space-separated list of node properties required (e.g., cl_two) or prohibited (e.g., ^cl_two) to run the job.

##### Loop options
Only used when job-type is **loop**.

`--loop-start` `INTEGER` — Starting cycle for a loop job. Defaults to **1**.

`--loop-end` `INTEGER` — Ending cycle for a loop job.

`--archive` `TEXT` — Directory name for archiving files from a loop job. Defaults to **storage**.

`--archive-format` `TEXT` — Filename format for archived files. Defaults to **job%04d**.

`--archive-mode` `TEXT` — Colon-, comma-, or space-separated list of archive modes controlling when working directory files are archived upon job completion. Supports the same modes as **--transfer-mode**. Defaults to **success**.

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

# qq ncpus 8
# qq ngpus 1
# qq walltime 1d

metamodule add ...
```

> In the example above, kebab-case is used for option names, but qq directives also support snake_case, camelCase, and PascalCase.  
> For example: `# qq job-type loop`, `# qq job_type loop`, `# qq jobType loop`, and `# qq JobType loop` are all equivalent.

Command-line options (*including list options since qq v0.8*) always take precedence over options defined in the script body.

### Examples

```bash
qq submit run_script.sh -q default --ncpus 8 --workdir scratch_local --worksize-per-cpu 2gb --walltime 2d --props hyperthreading
```

Submits the script `run_script.sh` to the `default` queue, requesting 8 CPU cores and 16 GB of local scratch space (2 GB per core). The requested walltime is 48 hours, and the job must run on a node with the `hyperthreading` property. Additional options may come from the script or queue defaults, but command-line options take precedence.

```bash
qq submit run_script.sh
```

Submits the script `run_script.sh`, taking all submission options from the script itself or from queue/server defaults.
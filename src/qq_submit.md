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
>   qq submit -q cpu run_script --ncpus=8 --walltime=12h --props=cl_zero
>   ```
>
> - Options can also be specified directly [in the submitted script](#specifying-options-in-the-script), or as a mix of in-script and command-line definitions. Command-line options always take precedence.
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

When the job is successfully submitted, `qq submit` creates a `.qqinfo` file for tracking the job’s state.

#### Options

##### General settings

`-q`, `--queue` `TEXT` — Name of the queue to submit the job to.  

`--account` `TEXT` — Account to use for the job. Required only in environments with accounting (e.g., IT4Innovations).  

`--job-type` `TEXT` — Type of the qq job. Defaults to `standard`.  

`--exclude` `TEXT` — A colon-, comma-, or space-separated list of files or directories that should **not** be copied to the working directory. Paths must be relative to the input directory.

`--include` `TEXT` — A colon-, comma-, or space-separated list of files or directories that **should** be copied to the working directory even though they are not part of the input directory. These files will **not** be copied back after the job finishes Paths may be absolute or relative to the input directory. Ignored if the input directory is used directly as the working directory.

`--depend` `TEXT` — Job dependency specification. You may provide one or more dependency expressions separated by commas or spaces. Each expression uses the format `<type>=<job_id>[:<job_id>...]`, e.g.: `after=1234`, `afterok=456:789`.

`--batch-system` `TEXT` — Name of the batch system to submit the job to. If not provided, qq will use the environment variable `QQ_BATCH_SYSTEM` or attempt to auto-detect the system.

##### Requested resources

`--nnodes` `INTEGER` — Number of compute nodes to allocate for the job.  

`--ncpus-per-node` `INTEGER` — Number of CPU cores to allocate per requested node.

`--ncpus` `INTEGER` — Total number of CPU cores to allocate for the job. Overrides `--ncpus-per-node`.

`--mem-per-cpu` `TEXT` — Memory per CPU core. Specify as `Nmb` or `Ngb` (e.g., `500mb`, `2gb`).  

`--mem-per-node` `TEXT` — Memory per node. Specify as `Nmb` or `Ngb` (e.g., `500mb`, `32gb`). Overrides `--mem-per-cpu`.

`--mem` `TEXT` — Total memory for the job. Specify as `Nmb` or `Ngb` (e.g., `500mb`, `64gb`). Overrides both `--mem-per-cpu` and `--mem-per-node`.

`--ngpus-per-node` `INTEGER` — Number of GPUs to allocate per requested node.

`--ngpus` `INTEGER` — Total number of GPUs to allocate for the job. Overrides `--ngpus-per-node`.

`--walltime` `TEXT` — Maximum allowed runtime for the job. Examples: `1d`, `12h`, `10m`, `24:00:00`, `12:00:00`, `00:10:00`.

`--work-dir`, `--workdir` `TEXT` — Working directory type for the job. Available types [depend on the environment](work_dir.md).

`--work-size-per-cpu`, `--worksize-per-cpu` `TEXT` — Storage per CPU core. Specify as `Ngb` (e.g., `1gb`).  

`--work-size-per-node`, `--worksize-per-node` `TEXT` — Storage per node. Specify as `Ngb` (e.g., `32gb`). Overrides `--work-size-per-cpu`.

`--work-size`, `--worksize` `TEXT` — Total storage for the job. Specify as `Ngb` (e.g., `64gb`). Overrides both `--work-size-per-cpu` and `--work-size-per-node`.

`--props` `TEXT` — Colon-, comma-, or space-separated list of required or prohibited node properties (e.g., `cl_two` or `^cl_two`).

##### Loop options  
*(Only used when `--job-type` is set to `loop`.)*

`--loop-start` `INTEGER` — Starting cycle number. Defaults to `1`.  

`--loop-end` `INTEGER` — Ending cycle number.  

`--archive` `TEXT` — Directory used for archiving loop-job files. Defaults to `storage`.  

`--archive-format` `TEXT` — Filename format for archived files. Defaults to `job%04d`.  

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
> For example: `# qq job-type loop`, `# qq job_type loop`, `# qq jobType loop`, and `# qq JobType loop` are all equivalent.

Command-line options always take precedence over options defined in the script body.

### Examples

```bash
qq submit run_script.sh -q default --ncpus=8 --workdir=scratch_local --worksize-per-cpu=2gb --walltime=2d --props=hyperthreading
```

Submits the script `run_script.sh` to the `default` queue, requesting 8 CPU cores and 16 GB of local scratch space (2 GB per core). The requested walltime is 48 hours, and the job must run on a node with the `hyperthreading` property. Additional options may come from the script or queue defaults, but command-line options take precedence.

```bash
qq submit run_script.sh
```

Submits the script `run_script.sh`, taking all submission options from the script itself or from queue/server defaults.
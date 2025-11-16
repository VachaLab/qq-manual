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

`--account` `TEXT` — Account to use for the job. Required only in environments with accounting systems (e.g., IT4Innovations).  

`--job-type` `TEXT` — Type of the qq job. Defaults to `standard`.  

`--exclude` `TEXT` — A colon-, comma-, or space-separated list of files or directories that should **not** be copied to the working directory. Paths to files and directories to exclude must be relative to the input directory.

`--include` `TEXT` —  A colon-, comma-, or space-separated list of files or directories that **should be** copied to the working directory even though they are not part of the job's input directory. These files will **not** be copied back to the input directory even after successful completion of the job. Paths to files and directories to include must be either absolute or relative to the input directory. This option is ignored if the input directory itself is used as the working directory.

`--depend` `TEXT` — Specify job dependencies. You can provide one or more dependency expressions separated by commas, spaces, or both. Each expression should follow the format `<type>=<job_id>[:<job_id>...]`, for example: `after=1234`, `afterok=456:789`.  

`--batch-system` `TEXT` — Name of the batch system to submit the job to. If not specified, qq will use the environment variable `QQ_BATCH_SYSTEM` or attempt to auto-detect it.  

##### Requested resources

`--nnodes` `INTEGER` — Number of compute nodes to allocate for the job.  

`--ncpus` `INTEGER` — Number of CPU cores to allocate for the job.  

`--mem-per-cpu` `TEXT` — Amount of memory to allocate per CPU core. Specify as `Nmb` or `Ngb` (e.g., `500mb`, `2gb`).  

`--mem` `TEXT` — Total memory to allocate for the job. Specify as `Nmb` or `Ngb` (e.g., `500mb`, `10gb`). Overrides `--mem-per-cpu`.  

`--ngpus` `INTEGER` — Number of GPUs to allocate for the job.  

`--walltime` `TEXT` — Maximum runtime allowed for the job.  

`--work-dir`, `--workdir` `TEXT` — Type of working directory to use for the job.  

`--work-size-per-cpu`, `--worksize-per-cpu` `TEXT` — Storage to allocate per CPU core. Specify as `Ngb` (e.g., `1gb`).  

`--work-size`, `--worksize` `TEXT` — Total storage to allocate for the job. Specify as `Ngb` (e.g., `10gb`). Overrides `--work-size-per-cpu`.  

`--props` `TEXT` — Colon-, comma-, or space-separated list of node properties required (e.g., `cl_two`) or prohibited (e.g., `^cl_two`) for the job.  

##### Loop options  
*(Only used when `job-type` is set to `loop`.)*

`--loop-start` `INTEGER` — Starting cycle number for a loop job. Defaults to `1`.  

`--loop-end` `INTEGER` — Ending cycle number for a loop job.  

`--archive` `TEXT` — Directory name used for archiving files in a loop job. Defaults to `storage`.  

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
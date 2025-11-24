# Configuration

qq is highly configurable. All user-adjustable options (colors, panel widths, timeouts, suffixes, environment variables, and batch-system behavior) are controlled through a single **TOML configuration file**.

qq automatically loads configuration from:

1. `$QQ_CONFIG` environment variable (highest priority)  
2. `qq_config.toml` (in the current directory)  
3. `${HOME}/.config/qq/config.toml` (default location, XDG-compatible)

If no file is found, qq falls back to built-in defaults.

> If you want the configuration to apply across the entire cluster, you must install it both on your desktop and in the home directories of all compute nodes. However, if you are only customizing qq's appearance (see [Themes](#themes)), placing the configuration file on your desktop will probably suffice.

## Configuration structure

The configuration file is a TOML document whose top-level tables correspond directly to qq’s internal configuration groups. You do **not** need to use all fields—omitted fields simply fall back to defaults.

An example of a tiny config file:

```toml
[state_colors]
running = "bright_green"
queued = "bright_yellow"
```

This configuration makes running jobs display in green (instead of the default blue) and queued jobs display in yellow (instead of the default purple). No other behavior is changed.

See all configurable options [below](#all-configurable-options).

## Themes

Does writing your own configuration seem to complex? qq provides few ready-to-use themes, available from [github.com/Ladme/qq/tree/main/themes](https://github.com/Ladme/qq/tree/main/themes).

Available themes include:

- **light_terminal** — By default, qq assumes a dark terminal background. On light backgrounds, the default output may look very ugly. If you use a light terminal background, you should install this qq theme.
- **traffic_lights_theme** — Adjusts the colors used for job states: running jobs are green (instead of blue), queued jobs are yellow (instead of purple), failed jobs are red (default color), and finished jobs are blue (instead of green).

You may import these themes directly or copy pieces into your own configuration.

## All configurable options

The following expanded TOML structure lists all available sections and fields. You can copy this into your config and modify only the pieces you care about.

> Note that we generally recommend modifying only qq's appearance (tables with `presenter` in name or the `state_colors` table). 
>
> Changing any of `suffixes`, `env_vars`, `date_formats`, `exit_codes`, `binary_name` is **dangerous** and may break qq's functionality.


```toml
##############################################
# File suffixes used by qq.
##############################################
[suffixes]
# Suffix for qq info files.
qq_info = ".qqinfo"
# Suffix for qq output files.
qq_out = ".qqout"
# Suffix for captured stdout.
stdout = ".out"
# Suffix for captured stderr.
stderr = ".err"


##############################################
# Environment variable names used by qq.
##############################################
[env_vars]
# Indicates job is running inside the qq environment.
guard = "QQ_ENV_SET"
# Enables qq debug mode.
debug_mode = "QQ_DEBUG"
# Path to the qq info file for the job.
info_file = "QQ_INFO"
# Machine from which the job was submitted.
input_machine = "QQ_INPUT_MACHINE"
# Submission directory path.
input_dir = "QQ_INPUT_DIR"
# Whether submission was from shared storage.
shared_submit = "QQ_SHARED_SUBMIT"
# Name of the batch system used.
batch_system = "QQ_BATCH_SYSTEM"
# Current loop-cycle index.
loop_current = "QQ_LOOP_CURRENT"
# Starting loop-cycle index.
loop_start = "QQ_LOOP_START"
# Final loop-cycle index.
loop_end = "QQ_LOOP_END"
# Non-resubmit flag returned by a job script.
no_resubmit = "QQ_NO_RESUBMIT"
# Archive filename pattern.
archive_format = "QQ_ARCHIVE_FORMAT"
# Scratch directory on Metacentrum clusters.
pbs_scratch_dir = "SCRATCHDIR"
# Slurm account used for the job.
slurm_job_account = "SLURM_JOB_ACCOUNT"
# Storage type for LUMI scratch.
lumi_scratch_type = "LUMI_SCRATCH_TYPE"
# Total CPUs used.
ncpus = "QQ_NCPUS"
# Total GPUs used.
ngpus = "QQ_NGPUS"
# Total nodes used.
nnodes = "QQ_NNODES"
# Walltime in hours.
walltime = "QQ_WALLTIME"


##############################################
# Timeout settings in seconds.
##############################################
[timeouts]
# Timeout for SSH in seconds.
ssh = 60
# Timeout for rsync in seconds.
rsync = 600


##############################################
# Settings for Runner (qq run) operations.
##############################################
[runner]
# Maximum number of attempts when retrying an operation.
retry_tries = 3
# Wait time (in seconds) between retry attempts.
retry_wait = 300
# Delay (in seconds) between sending SIGTERM and SIGKILL to a job script.
sigterm_to_sigkill = 5
# Interval (in seconds) between successive checks of the running script's state.
subprocess_checks_wait_time = 2


##############################################
# Settings for Archiver operations.
##############################################
[archiver]
# Maximum number of attempts when retrying an operation.
retry_tries = 3
# Wait time (in seconds) between retry attempts.
retry_wait = 300


##############################################
# Settings for Goer (qq go) operations.
##############################################
[goer]
# Interval (in seconds) between successive checks of the job's state
# (when waiting for the job to start).
wait_time = 5


##############################################
# Settings for qq loop jobs.
##############################################
[loop_jobs]
# Pattern used for naming loop jobs.
pattern = "+%04d"


##############################################
# Settings for Presenter (qq info).
##############################################
[presenter]
# Style used for the keys in job status/info panel.
key_style = "default bold"
# Style used for values in job status/info panel.
value_style = "white"
# Style used for notes in job status/info panel.
notes_style = "grey50"

[presenter.job_status_panel]
# Maximal width of the job status panel.
max_width = null
# Minimal width of the job status panel.
min_width = 60
# Style of the border lines.
border_style = "white"
# Style of the title.
title_style = "white bold"

[presenter.full_info_panel]
# Maximal width of the job info panel.
max_width = null
# Minimal width of the job info panel.
min_width = 80
# Style of the border lines.
border_style = "white"
# Style of the title.
title_style = "white bold"
# Style of the separators between individual sections of the panel.
rule_style = "white"


##############################################
# Settings for JobsPresenter (qq jobs/stat).
##############################################
[jobs_presenter]
# Maximal width of the jobs panel.
max_width = null
# Minimal width of the jobs panel.
min_width = 80
# Maximum displayed length of a job name before truncation.
max_job_name_length = 20
# Maximum displayed length of working nodes before truncation.
max_nodes_length = 40
# Style used for border lines.
border_style = "white"
# Style used for the title.
title_style = "white bold"
# Style used for table headers.
headers_style = "default"
# Style used for table values.
main_style = "white"
# Style used for job statistics.
secondary_style = "grey70"
# Style used for extra notes.
extra_info_style = "grey50"
# Style used for strong warning messages.
strong_warning_style = "bright_red"
# Style used for mild warning messages.
mild_warning_style = "bright_yellow"
# List of columns to show in the output.
# If not set, the settings for the current batch system will be used.
columns_to_show = null
# Code used to signify "total jobs".
sum_jobs_code = "Σ"


##############################################
# Settings for QueuesPresenter (qq queues).
##############################################
[queues_presenter]
# Maximal width of the queues panel.
max_width = null
# Minimal width of the queues panel.
min_width = 80
# Style used for border lines.
border_style = "white"
# Style used for the title.
title_style = "white bold"
# Style used for table headers.
headers_style = "default"

# Style used for the mark if the queue is available.
available_mark_style = "bright_green"
# Style used for the mark if the queue is not available.
unavailable_mark_style = "bright_red"
# Style used for the mark if the queue is dangling.
dangling_mark_style = "bright_yellow"

# Style used for information about main queues.
main_text_style = "white"
# Style used for information about reroutings.
rerouted_text_style = "grey50"

# Code used to signify "other jobs".
other_jobs_code = "O"
# Code used to signify "total jobs".
sum_jobs_code = "Σ"


##############################################
# Settings for NodesPresenter (qq nodes).
##############################################
[nodes_presenter]
# Maximal width of the nodes panel.
max_width = null
# Minimal width of the nodes panel.
min_width = 80
# Maximal width of the shared properties section.
max_props_panel_width = 40
# Style used for border lines.
border_style = "white"
# Style used for the title.
title_style = "white bold"
# Style used for table headers.
headers_style = "default"
# Style of the separators between individual sections of the panel.
rule_style = "white"
# Name to use for the leftover nodes that were not assigned to any group.
others_group_name = "other"
# Name to use for the group if it contains all nodes.
all_nodes_group_name = "all nodes"

# Style used for main information about the nodes.
main_text_style = "white"
# Style used for statistics and shared properties.
secondary_text_style = "grey70"
# Style used for the mark and resources if the node is free.
free_node_style = "bright_green bold"
# Style used for the mark and resources if the node is partially free.
part_free_node_style = "green"
# Style used for the mark and resources if the node is busy.
busy_node_style = "blue"
# Style used for all information about unavailable nodes.
unavailable_node_style = "bright_red"


##############################################
# Date and time format strings.
##############################################
[date_formats]
# Standard date format used by qq.
standard = "%Y-%m-%d %H:%M:%S"
# Date format used by PBS Pro.
pbs = "%a %b %d %H:%M:%S %Y"
# Date format used by Slurm.
slurm = "%Y-%m-%dT%H:%M:%S"


##############################################
# Exit codes used for various errors.
##############################################
[exit_codes]
# Returned when a qq script is run outside the qq environment.
not_qq_env = 90
# Default error code for failures of qq commands or most errors in the qq environment.
default = 91
# Returned when a qq job fails and its error state cannot be written to the qq info file.
qq_run_fatal = 92
# Returned when a qq job fails due to a communication error between qq services.
qq_run_communication = 93
# Used by job scripts to signal that a loop job should not be resubmitted.
qq_run_no_resubmit = 95
# Returned on an unexpected or unhandled error.
unexpected_error = 99


##############################################
# Color scheme for states display.
##############################################
[state_colors]
# Style used for queued jobs.
queued = "bright_magenta"
# Style used for held jobs.
held = "bright_magenta"
# Style used for suspended jobs.
suspended = "bright_black"
# Style used for waiting jobs.
waiting = "bright_magenta"
# Style used for running jobs.
running = "bright_blue"
# Style used for booting jobs.
booting = "bright_cyan"
# Style used for killed jobs.
killed = "bright_red"
# Style used for failed jobs.
failed = "bright_red"
# Style used for finished jobs.
finished = "bright_green"
# Style used for exiting jobs.
exiting = "bright_yellow"
# Style used for jobs in an inconsistent state.
in_an_inconsistent_state = "grey70"
# Style used for jobs in an unknown state.
unknown = "grey70"
# Style used whenever a summary of jobs is provided.
sum = "white"
# Style used for "other" job states.
other = "grey70"


##############################################
# Options associated with the Size dataclass.
##############################################
[size]
# Maximal relative error acceptable when rounding Size values for display.
max_rounding_error = 0.1


##############################################
# Options associated with PBS.
##############################################
[pbs_options]
# Name of the subdirectory inside SCRATCHDIR used as the job's working directory.
scratch_dir_inner = "main"


##############################################
# Options associated with Slurm.
##############################################
[slurm_options]
# Maximal number of threads used to collect information about jobs using scontrol.
jobs_scontrol_nthreads = 8


##############################################
# Options associated with Slurm on IT4I clusters.
##############################################
[slurm_it4i_options]
# Number of attempts when preparing a working directory on scratch.
scratch_dir_attempts = 3


##############################################
# Options associated with Slurm on LUMI.
##############################################
[slurm_lumi_options]
# Number of attempts when preparing a working directory on scratch.
scratch_dir_attempts = 3


##############################################
# General configuration
##############################################
# Name of the qq binary.
binary_name = "qq"
```

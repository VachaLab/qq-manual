# qq sync

The `qq sync` command fetches files from a job's working directory to its input directory. It is qq's equivalent of Infinity's `psync`.

***

> **Quick comparison with psync**
> - Unlike `psync`, `qq sync` fetches **all files** from the working directory by default.
> - You can use `qq sync` with a job ID to fetch files from the job's working directory to its input directory without having to actually navigate to its input directory. You can even provide IDs of multiple jobs.
> - You can provide `qq sync` with one or more directories to search for qq jobs in. Files for all discovered jobs are then fetched from their working directories.
> - If you want to fetch only specific files, you cannot select them interactively — you must provide a list of filenames when running `qq sync`.

***

### Description

Fetches files from the working directories of the specified qq jobs, or from the working directories of the jobs submitted from the specified directories.

```bash
qq sync [OPTIONS] JOB_ID...
```

**JOB_ID.,.** — One or more IDs of jobs whose working directory files should be fetched. Optional

If no `JOB_ID` and no directory are provided, `qq sync` searches for qq jobs in the current directory. If multiple suitable jobs are found, `qq sync` fetches files from each one in turn. Files fetched from later jobs may overwrite files from earlier ones in the input directory.

**Files are copied from the job's working directory to its input directory, not to the current directory.**

#### Options

- `-d`, `--dir` — One or more directories to search for qq jobs in. Supports globs.
 
- `-a`, `--all` — Fetch files for all your unfinished jobs.
 
- `-s`, `--server` — Operate on jobs from the specified batch server. If not specified, the current server is used. Only used with `--all`.

- `-f`, `--files` `TEXT` — A colon-, comma-, or space-separated list of files and directories to fetch. If not specified, the entire content of the working directory is fetched.

### Examples

```bash
qq sync 123456
```

Fetches all files from the working directory of the job with ID `123456` to that job's input directory. If you use just the numerical portion of the job ID, the job is assumed to be located on the default batch server. If the job is located on a [different batch server](../servers.md#qq-info-qq-go-qq-kill-qq-sync-qq-wipe-qq-respawn), you need to use the full ID including the server address.

This command only works if the specified job is a qq job with a valid and accessible info file, and if the batch server and main node are reachable from the current machine.

***

```bash
qq sync 123456 144844 156432
```

Fetches all files from the working directories of jobs `123456`, `144844`, and `156432` to their respective input directories.

***

```bash
qq sync
```

Fetches all files from the working directories of all jobs whose info files are present in the current directory.

***

```bash
qq sync 123456 -f file1.txt,file2.txt,file3.txt
```

Fetches `file1.txt`, `file2.txt`, and `file3.txt` from the working directory of the job with ID `123456` to its input directory. All other files are ignored. Missing files are skipped without error.

***

```bash
qq sync -d /path/to/dir
```

Feteches all files from the working directories of all jobs whose info files are present in directory `/path/to/dir`.

***

```bash
qq sync -d /path/to/job*
```

Fetches all files from the working directories of all jobs located in directories matching the glob pattern `/path/to/job*` (e.g., `/path/to/job1`, `/path/to/job2`, `/path/to/job3`). Files are always fetched to the corresponding job's input directory. Useful for managing job collections.

***

```bash
qq sync --all
```

Fetches files for all your running jobs.

***

```bash
qq sync --all --server meta
```

Fetches files for all your running jobs associated with the Metacentrum batch server.

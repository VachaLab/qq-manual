# qq sync

The `qq sync` command fetches files from a job's working directory to its input directory. It is qq's equivalent of Infinity's `psync`.

***

> **Quick comparison with psync**
> - Unlike `psync`, `qq sync` fetches **all files** from the working directory by default.
> - If you want to fetch only specific files, you cannot select them interactively — you must provide a list of filenames when running `qq sync`.

***

### Description

Fetches files from the working directory of the specified qq job, or from the working directory of the job submitted from the current directory.

```bash
qq sync [OPTIONS] JOB_ID
```

**JOB_ID** — Identifier of the job whose working directory files should be fetched. This argument is optional.

If `JOB_ID` is not provided, `qq sync` searches for qq jobs in the current directory. If multiple suitable jobs are found, `qq sync` fetches files from each one in turn. Files fetched from later jobs may overwrite files from earlier ones in the input directory.

**Files are copied from the job's working directory to its input directory, not to the current directory.**

#### Options

`-f`, `--files` `TEXT` — A colon-, comma-, or space-separated list of files and directories to fetch. If not specified, the entire content of the working directory is fetched.

### Examples

```bash
qq sync 123456
```

Fetches all files from the working directory of the job with ID `123456` to that job's input directory. You can use either the short job ID or the full ID including the batch server address. This only works if the specified job is a qq job with a valid and accessible info file, and if the batch server and main node are reachable from the current machine.

```bash
qq sync
```

Fetches all files from the working directories of all jobs whose info files are present in the current directory.

```bash
qq sync 123456 -f file1.txt,file2.txt,file3.txt
```

Fetches `file1.txt`, `file2.txt`, and `file3.txt` from the working directory of the job with ID `123456` to its input directory. All other files are ignored. Missing files are skipped without error.
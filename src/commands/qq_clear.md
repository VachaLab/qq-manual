# qq clear

The `qq clear` command is used to remove qq runtime files from the current or specified directory or directories. It is qq's equivalent of Infinity's `premovertf`.

***

> **Quick comparison with premovertf**  
> - `qq clear` checks whether the qq runtime files belong to an **active** or **successfully completed** qq job.
>   - If they **do**, the files are **not** deleted (if you *really* want to delete them, you have to use the `--force` flag).  
>   - If they do **not**, the files are deleted without asking for confirmation.  
>   - In contrast, `premovertf` simply lists the files and always asks for confirmation before deleting them (unless run as `premovertf -f`).
> - `qq clear` can operate on a specific directory or even multiple directories at once using the `-d`/`--dir` option.

***

### Description

Deletes qq runtime files from the current directory or specified directories.

```bash
qq clear [OPTIONS]
```

#### Options

- `-d`, `--dir` — One or more directories from which to clear qq runtime files. Supports globs.

- `--force` — Force deletion of all qq runtime files, even if they belong to active or successfully completed jobs.

### Examples

```bash
qq clear
```

Deletes all qq runtime files (files with extensions `.out`, `.err`, `.qqinfo`, `.qqout`) from the current directory, provided these files are not associated with any job or belong to a job that has been killed or has failed. If multiple jobs are represented in the directory, only files related to killed or failed jobs are deleted. This helps prevent accidental removal of files from running or successfully finished jobs.

***

```bash
qq clear -d gromacs/popc/job1
```

Deletes all *suitable* qq runtime files from directory corresponding to the relative path `gromacs/popc/job1`.

***

```bash
qq clear -d gromacs/popc/job*
```

Deletes all *suitable* qq runtime files from directories corresponding to paths matching the glob pattern `gromacs/popc/job*` (e.g., `gromacs/popc/job1`, `gromacs/popc/job2`, `gromacs/popc/job3`).

***

```bash
qq clear --force
```

Deletes all qq runtime files from the current directory, regardless of their job state. In other words, all files with extensions `.out`, `.err`, `.qqinfo`, and `.qqout` will be removed. **This is dangerous — only use the `--force` flag if you are absolutely sure you know what you are doing!**

### Notes

- You should not delete the `.qqinfo` file of a running job, as **this will cause the job to fail!** If you use just `qq clear` without `--force`, you don't need to worry about accidentally deleting it, since `qq` will only delete files that are safe to be deleted.

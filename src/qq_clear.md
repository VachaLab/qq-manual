# qq clear

The `qq clear` command is used to remove qq runtime files from the current directory. It is qq's equivalent of Infinity's `premovertf`.

***

> **Quick comparison with premovertf**  
> - `qq clear` checks whether the qq runtime files belong to an **active** or **successfully completed** qq job.
>   - If they **do**, the files are **not** deleted (if you *really* want to delete them, you have to use the `--force` flag).  
>   - If they do **not**, the files are deleted without asking for confirmation.  
>   - In contrast, `premovertf` simply lists the files and always asks for confirmation before deleting them (unless run as `premovertf -f`).

***

### Description

Deletes qq runtime files from the current directory.

```bash
qq clear [OPTIONS]
```

#### Options

`--force` — Force deletion of all qq runtime files, even if they belong to active or successfully completed jobs.

### Examples

```bash
qq clear
```

Deletes all qq runtime files (files with extensions `.out`, `.err`, `.qqinfo`, `.qqout`) from the current directory, provided these files are not associated with any job or belong to a job that has been killed or has failed. If multiple jobs are represented in the directory, only files related to killed or failed jobs are deleted. This helps prevent accidental removal of files from running or successfully finished jobs.

```bash
qq clear --force
```

Deletes all qq runtime files from the current directory, regardless of their job state. In other words, all files with extensions `.out`, `.err`, `.qqinfo`, and `.qqout` will be removed. **This is dangerous — only use the `--force` flag if you are absolutely sure you know what you are doing!**

### Notes

- You should not delete the `.qqinfo` file of a running job, as **this will cause the job to fail!**

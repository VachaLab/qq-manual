# qq cd

The `qq cd` command is used to navigate to the input directory of a job. It is qq's equivalent of Infinity's `pgo` when used with a job ID.

***

> **Quick comparison with pgo**  
> - Unlike `pgo`, `qq cd` does not have a dual function.  
>   - `pgo` can either open a new shell on the job's main node or navigate to the job's input directory depending on the arguments provided.  
>   - `qq cd`, on the other hand, always navigates to the **input directory** of the specified job in the current shell. It never opens a new shell.
>   - If you want to open a shell in the job's working directory instead, use [`qq go`](qq_go.md).

***

### Description

Changes the current working directory to the input directory of the specified job.

```bash
qq cd [OPTIONS] JOB_ID
```

**JOB_ID** — Identifier of the job whose input directory should be entered.

### Examples

```bash
qq cd 123456
```

Changes the current shell's working directory to the input directory of the job with ID `123456`. You can use either the short job ID or the full ID including the batch server address.

### Notes

- Works with any job type, including those not submitted using `qq submit`.
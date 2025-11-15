# Runtime files

qq uses four types of runtime files, each with one of the following extensions: `.qqinfo`, `.out`, `.err`, and `.qqout`.

### qqinfo files

A `.qqinfo` file (also called a "qq info file") is created after submission by [`qq submit`](qq_submit.md). It stores information used to track the job submitted from that directory. Each qq job requires its own info file for management and control.

> **Do NOT move, modify, or delete qq info files manually.**  
> Always use qq commands such as [`qq kill`](qq_kill.md) or [`qq clear`](qq_clear.md) to manage them safely.  
> Moving, editing, or removing a qq info file while a job is running **will** cause the job to crash, and you may lose its data.

### out files

A `.out` file contains the **standard output** from the script executed as a qq job. This file is created when the job starts running in the working directory and is copied to the input directory once the job is completed.

### err files

A `.err` file contains the **standard error output** from the script executed as a qq job. Like the `.out` file, it is created when the job starts running in the working directory and is copied to the input directory once the job is completed.

### qqout files

A `.qqout` file contains the output from the [`qq run`](qq_run.md) execution environment. It includes technical information about the job's progress and internal qq operations. If your batch system is PBS, this file is only placed into the input directory after the job is completed. If your batch system is Slurm, this file is available after the job starts running.
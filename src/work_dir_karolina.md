# Karolina supercomputer

On the Karolina supercomputer, the working directory is, by default, created inside your project directory on the shared scratch storage. You can, however, also choose to use the input directory itself as the working directory.

To control where the working directory is created, use the `work-dir` option (or the equivalent spelling `workdir`) of the [`qq submit`](qq_submit.md) command:

- `--work-dir scratch` – **Default option** on Karolina. Creates the working directory on the shared scratch storage.  
- `--work-dir input_dir` – Uses the input directory itself as the working directory. Files are not copied anywhere. If you use this option, it is **strongly** recommended to submit from the scratch storage.  
- `--work-dir job_dir` – Same as `input_dir`.

> **Recommendation:**
> - Submit jobs from your project's mounted storage (`/mnt/...`). With the default `--work-dir` option, qq automatically copies your data to scratch, executes the job there, and then copies the results back to your input directory.  
> - The size of the working directory on Karolina is limited by your filesystem quota, so you do not need to specify the `work-size` option.
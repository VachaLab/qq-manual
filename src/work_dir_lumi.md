# LUMI supercomputer

On the LUMI supercomputer, the working directory is, by default, created inside your project directory on the shared scratch storage. You can, however, also choose to create the working directory on the flash storage or use the input directory itself as the working directory.

To control where the working directory is created, use the `work-dir` option (or the equivalent spelling `workdir`) of the [`qq submit`](qq_submit.md) command:

- `--work-dir=scratch` – **Default option** on LUMI. Creates the working directory on the shared scratch storage.
- `--work-dir=flash` – Creates the working directory on the shared flash storage. This storage can be faster for I/O-heavy jobs. Note that on LUMI, you are billed for the amount of storage you use and flash storage is **much more expensive** than scratch storage!
- `--work-dir=input_dir` – Uses the input directory itself as the working directory. Files are not copied anywhere. If you use this option, it is recommended to submit from the scratch or flash storage.
- `--work-dir=job_dir` – Same as `input_dir`.

> **Recommendations:**
> - Submit jobs from your project's space (`/project/<project_id>`). With the default `--work-dir` option, qq automatically copies your data to scratch, executes the job there, and then copies the results back to your input directory.
> - The size of the working directory on LUMI is limited by your filesystem quota, so you do not need to specify the `work-size` option.
> - **IMPORTANT!** On LUMI, you are billed for the amount of storage you use! qq only clears the working directory if the job is successfully completed. If your job fails, you MUST clear the working directory yourself, otherwise you will get billed for it!

***

For more details on storage types available on LUMI, visit the [official documentation](https://docs.lumi-supercomputer.eu/storage/).

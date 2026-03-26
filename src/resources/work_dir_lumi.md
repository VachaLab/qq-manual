# LUMI supercomputer

On the LUMI supercomputer, the working directory is, by default, created inside your project directory on the shared scratch storage. You can, however, also choose to create the working directory on the flash storage or use the input directory itself as the working directory.

To control where the working directory is created, use the `work-dir` option (or the equivalent spelling `workdir`) of the [`qq submit`](../commands/qq_submit.md) command:

- `--work-dir scratch` – **Default option** on LUMI (*purely for consistency with the behavior of qq in other environments*). Creates the working directory on the shared scratch storage.
- `--work-dir flash` – Creates the working directory on the shared flash storage. This storage can be faster for I/O-heavy jobs. Note that on LUMI, you are billed for the amount of storage you use and flash storage is much more expensive than scratch storage!
- `--work-dir input_dir` – **Recommended option**. Uses the input directory itself as the working directory. Files are not copied anywhere. If you use this option, you should submit the job from the scratch or flash storage.
- `--work-dir job_dir` – Same as `input_dir`.

> **Recommendations:**
> - **Submit jobs from your project's scratch (`/scratch/<project_id>`) and using the option `--workdir input_dir`!**
> - On LUMI, you are billed for the amount of storage you use! Try to avoid storing large amounts of data in the project's storages.

***

For more details on storage types available on LUMI, visit the [official documentation](https://docs.lumi-supercomputer.eu/storage/).

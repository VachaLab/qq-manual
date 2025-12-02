# Clusters of the Metacentrum family

On clusters of the Metacentrum family (such as robox, sokar, and of course all Metacentrum clusters), the working directory is, by default, created on the local scratch storage of the main compute node assigned to the job. You can, however, explicitly choose to use SSD scratch, shared scratch, in-memory scratch (if available), or even use the input directory itself as the working directory.

To control where the working directory is created, use the `work-dir` option (or the equivalent spelling `workdir`) of the [`qq submit`](qq_submit.md) command:

- `--work-dir=scratch_local` – **Default option** on Metacentrum-family clusters. Creates the working directory on an appropriate local scratch storage. Depending on the setup, it may also be created on SSD scratch.
- `--work-dir=scratch_ssd` – Creates the working directory on SSD-based scratch storage.
- `--work-dir=scratch_shared` – Creates the working directory on shared scratch storage accessible by multiple nodes.
- `--work-dir=scratch_shm` – Creates the working directory in RAM (in-memory scratch). Useful for jobs requiring extremely fast I/O. Note that if your job fails, your data are immediately lost.
- `--work-dir=input_dir` – Uses the input directory itself as the working directory. Files are not copied anywhere. Can be slower for I/O-heavy jobs.
- `--work-dir=job_dir` – Same as `input_dir`.

> Not all scratch types are available on every compute node. Use [`qq nodes`](qq_nodes.md) to see which storage options are supported by each node.

For more details on scratch storage types available on Metacentrum-family clusters, visit the [official documentation](https://docs.metacentrum.cz/en/docs/computing/infrastructure/scratch-storages).

## Specifying the working directory size

### Local, SSD, and shared scratch
By default, qq allocates **1 GB of storage per CPU core** when using a scratch directory. If you need a different amount of storage, you can adjust it using the following [`qq submit`](qq_submit.md) options:

- `--work-size-per-cpu` (or `--worksize-per-cpu`) — specifies the amount of storage per requested CPU core.
- `--work-size-per-node` (or `--worksize-per-node`) — specifies the amount of storage per requested compute node.
- `--work-size` (or `--worksize`) — specifies the total amount of storage for the entire job.

> `--work-size-per-node` overrides `--work-size-per-cpu`. `--work-size` overrides both `--work-size-per-cpu` and `--work-size-per-node`.

**Example:**

```bash
qq submit --work-size=16gb (...)
# or
qq submit --work-size-per-cpu=2gb (...)
```

### In-memory scratch
If you use `--work-dir=scratch_shm`, you should allocate memory instead of `work-size`, using the `mem`, `mem-per-node`, or `mem-per-cpu` options. Make sure the total allocated memory covers both your program’s memory usage **and** your in-memory storage needs. By default, qq allocates **1 GB of RAM per CPU core** for all jobs.

> `--mem-per-node` overrides `--mem-per-cpu`. `--mem` overrides both `--mem-per-cpu` and `--mem-per-node`.

**Example:**

```bash
qq submit --mem=32gb (...)
# or
qq submit --mem-per-cpu=4gb (...)
```

### Not requesting scratch
If you use `--work-dir=input_dir` (or `--work-dir=job_dir`), the available storage is limited by your shared filesystem quota.
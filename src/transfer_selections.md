# Controlling what is transferred between the input and working directory

By default, qq [prepares a working directory on the compute node](job_types/standard_job.md#2-preparing-the-working-directory) and copies everything from the input directory into it, except for the qq info file and, in the case of a loop job, the archive directory. It then executes the script and transfers all the files back.

But what if some files in the input directory should not be copied to the working directory, because they are too large or simply not needed for the job? What if you need files that live outside the input directory? Or what if the script produces files that you do not want copied back? This is what the submission options `exclude`, `include`, and `ignore` are for.

## Submission option `exclude`

With this option you select any number of files or directories in the input directory that should not be transferred to the working directory. Items in the list are separated by colons, commas, or spaces. Paths must be either absolute or relative to the input directory. Glob patterns are allowed, but note that they are evaluated at submission time.

```bash
qq submit -q default (...) --exclude file1.txt,file2.txt
```

While exclude forbids transfer to the working directory, if your executed script creates a file matching the excluded name, it is transferred back to the input directory. If your script creates a directory, it will be merged with the same directory in the input directory.

> [!IMPORTANT]
> Glob patterns are expanded when the job is submitted, not when it runs. For a loop job, they are expanded once at the original submission and the resulting list of files is reused in every cycle, so files created later will not be picked up.

## Submission option `include`

With this option you select any number of files or directories outside the input directory that should also be placed in the working directory. They are specified in the same way as for `exclude`.

```bash
qq submit -q default (...) --include ../shared/*.itp
```

Included files are **not** transferred back after the script finishes, so this option is meant for read-only files. If you need to modify them, copy them into the input directory before submission.

This also gives you a way to stop specific files in the input directory from being copied back. If you explicitly include a file that will be created in the working directory, it will not be transferred back from the working directory, so any changes the script makes to it are discarded. That may be occasionally useful.

## Submission option `ignore`

With this option you select any number of files and directories that should be left out of every transfer operation. They are specified in the same way as for the two options above.

```bash
qq submit -q default (...) --ignore irrelevant_output.dat
```

Ignored files are neither copied to the working directory, nor transferred back from it if your script generates them.

> [!NOTE]
> Everything described above only applies when the working directory is separate from the input directory. With `--work-dir input_dir` there is no transfer between the two, so `exclude`, `include`, and `ignore` have no effect there. They do still apply to the archival operations of loop jobs, which are described in the next section.

## Transfers to and from the archive of a loop job

qq [loop jobs](job_types/loop_job.md) create an archive directory where they automatically store files matching the archive pattern. In each cycle, qq copies the archived files matching the pattern for the current cycle into the working directory, making them available to the executed script.

The options `exclude`, `include`, and `ignore` also control what is stored in the archive and what is fetched from it.

Use `exclude` to select files that should not be fetched from the archive into the working directory, even if they match the current archive pattern. A file of the same name generated in the working directory can still be archived.

```bash
qq submit -q default (...) --exclude storage/job0002.init
```

Use `include` to select files from the archive that should always be fetched, no matter the cycle. Files included this way are not copied back to the archive from the working directory.

```bash
qq submit -q default (...) --include storage/shared.init
```

Use `ignore` to select files that are neither transferred to the archive nor fetched from it.

```bash
qq submit -q default (...) --ignore storage/analysis_script.py
```

## Summary table

| Option         | Input → working                            | Working → input | Archive → working                                                  | Working → archive                            |
| -------------- | ------------------------------------------ | --------------- | ------------------------------------------------------------------ | -------------------------------------------- |
| none (default) | yes                                        | yes             | yes, if the file matches the archive pattern for the current cycle | yes, if the file matches the archive pattern |
| `exclude`      | no                                         | yes             | no, even if the file matches the current archive pattern           | yes, if the file matches the archive pattern |
| `include`      | yes, also from outside the input directory | no              | yes, in every cycle regardless of the pattern                      | no                                           |
| `ignore`       | no                                         | no              | no                                                                 | no                                           |

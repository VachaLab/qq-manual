# qq run

The `qq run` command represents the execution environment in which a qq job runs. It is qq's equivalent of Infinity's `infex` script and the `infinity-env`.

You should not invoke `qq run` directly. Instead, every script submitted with [`qq submit`](qq_submit.md) must include the following shebang line:

```bash
#!/usr/bin/env -S qq run
```

For more details about what `qq run` does, see the sections on [standard jobs](standard_job.md) and [loop jobs](loop_job.md).

***

> **Quick comparison with infex and infinity-env**
> - Like `infinity-env`, using the `qq run` shebang prevents you from accidentally running the script directly.
> - Unlike Infinity, **all** qq jobs must use this execution environment — no separate helper run script is created when submitting a qq job.
> - `qq run` also takes over the responsibilities of `parchive` and `presubmit`, which have no direct equivalents in qq.
# qq run

The execution environment running the qq job. qq's equivalent of Infinity's `infinity-env`.

You should not (and cannot) use qq run directly, but all scripts submitted using `qq submit` must contain the following shebang calling it:

```bash
#!/usr/bin/env -S qq run
```

> **Quick comparison with infinity-env**
> - Just like `infinity-env`, using the `qq run` shebang protects you from inadvertently running the script directly.
> - Unlike with Infinity, **all** qq jobs must use this execution environment.
> - `qq run` also takes the resposibilities of `parchive` and `presubmit`, which direct equivalents do not exist in qq.
# Specifying resubmission hosts

When running a [continuous](job_types/continuous_job.md) or a [loop](job_types/loop_job.md) job, each new cycle of the job is, by default, resubmitted either from the working node (where the job was running) or from the input machine (where the job was submitted from), depending on the batch system used. Currently, on Metacentrum-family clusters, the resubmission occurs from the input machine, while on Karolina and LUMI, the resubmission occurs from the working node.

This default behavior can be overridden by using the `--resubmit-from` option of [`qq submit`](commands/qq_submit.md):

```bash
qq submit -q default --ncpus 8 --job-type continuous --resubmit-from st1 job.sh
```

With this setting, all new cycles of the continuous job will be resubmitted from the `st1` node, regardless of where they were originally submitted from. **Note that `qq` does NOT need to be installed on the resubmission host, so you can use almost any computer with access to the batch server.**

## Resubmission hosts

To specify a resubmission host, you can either use its **hostname** or one of two special values: `input` or `working`.

If you specify the hostname directly, `qq` will connect to that host and resubmit the job from there. If you specify `input`, `qq` will connect to the input (submission) machine and resubmit the job from there. If you specify `working`, `qq` will not connect anywhere and will instead resubmit the job directly from the main working node on which the job is running.

## Specifying multiple resubmission hosts

You can specify multiple resubmission hosts by separating them with commas, colons, or spaces. `qq` will primarily attempt to resubmit the job from the first host in the list and will fall back to the next host if the first one is unavailable. Note that each connection is attempted multiple times with a delay between attempts to accommodate transient network issues.

```bash
qq submit (...) --resubmit-from input,st1,working
```

With this setting, `qq` will first attempt to resubmit the job from the `input` node. If that is unavailable, it will fall back to `st1`, and if that is also unavailable, it will fall back to `working`.

## Specifying resubmission hosts in a config file

You can globally configure the resubmission hosts in your [qq configuration file](config.md):

```toml
[resubmitter]
default_resubmit_hosts = "input,st1,working"
```

> [!NOTE]
> You only need to make this configuration file available on the original input machine from which the job is submitted. The settings will be transferred to the compute nodes and to the eventual resubmission host.

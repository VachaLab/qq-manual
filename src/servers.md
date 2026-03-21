# Submitting to non-default clusters

Clusters of the Metacentrum family (Robox, Sokar, and Metacentrum clusters) use a compatible environment and are somewhat interconnected. Consequently, while connected to a computer of the Robox cluster, you can reach not just the default Robox batch server, but also the batch servers of Sokar and Metacentrum. You can therefore monitor, submit, kill, and manage jobs on all of these clusters directly from your Robox desktop.

## Supported servers

The supported Metacentrum-family batch servers are:
- `robox-pro.ceitec.muni.cz` (default for the Robox cluster)
- `sokar-pbs.ncbr.muni.cz` (default for the Sokar cluster)
- `pbs-m1.metacentrum.cz` (default for the Metacentrum clusters)

You can provide any of these server names as an option to any qq command that supports it — namely, [qq jobs](commands/qq_jobs.md), [qq stat](commands/qq_state.md), [qq queues](commands/qq_queues.md), [qq nodes](commands/qq_nodes.md), and [qq submit](command/qq_submit.md).

Alternatively, you can use one of the following shortcuts:
- `robox`, which expands to `robox-pro.ceitec.muni.cz`
- `sokar`, which expands to `sokar-pbs.ncbr.muni.cz`
- `metacentrum` or `meta`, which both expand to `pbs-m1.metacentrum.cz`

> Note that **not** all batch servers are necessarily accessible from all machines. For instance, from the Sokar frontend you cannot connect to `robox-pro.ceitec.muni.cz`. However, all of the above batch servers should be reachable from any Robox desktop.

## [qq jobs](commands/qq_jobs.md), [qq stat](commands/qq_stat.md), [qq queues](commands/qq_queues.md), [qq nodes](commands/qq_nodes.md)

You can retrieve information about jobs, queues, and nodes associated with a different batch server by specifying the `--server` option (or its short form `-s`).

For example, when connected to a Robox computer, you can list your active jobs submitted to the Metacentrum clusters using:

```bash
qq jobs --server pbs-m1.metacentrum.cz
```

or using a shortcut:

```bash
qq jobs --server meta
```

Similarly, you can get information about all jobs of all users on the Sokar cluster:

```bash
qq stat -s sokar --all
```

> When you run `qq jobs` or `qq stat` without specifying a server (so that the jobs are collected for the default server), the "Job ID" column shows only the numerical portion of the job ID. When you run these commands **with** a server specified, you get the full job ID including the batch server address. This can be useful for the commands described [below](#qq-info-qq-go-qq-kill-qq-sync-qq-wipe).

You can also get information about queues and compute nodes available on another server:
```bash
qq queues -s <server-name>
qq nodes  -s <server-name>
```

## [qq submit](commands/qq_submit.md)

> ⚠️ This feature is experimental and may be unstable. Tread carefully and [report](https://github.com/VachaLab/qq/issues) any issues or suspicious behavior you encounter.

Apart from monitoring jobs on different servers, you can also submit jobs to them. To do so, specify the `--server` (`-s`) option when submitting the job.

For example, you can submit a job from a Robox desktop to the Sokar cluster like this:

```bash
qq submit -q default --ncpus 8 --walltime 12h --server sokar my_job.sh
```

*Note that you are submitting to a queue on the Sokar cluster, so you need to use a queue that is available there.*

> **Important note:** If you submit a job to a different cluster, you need to have [qq installed on this cluster](installation.md)!

## [qq info](commands/qq_info.md), [qq go](commands/qq_go.md), [qq kill](commands/qq_kill.md), [qq sync](commands/qq_sync.md), [qq wipe](commands/qq_wipe.md)

You can operate on jobs submitted to another server. When you run any of these commands without an argument, the command operates on the job submitted from the current directory — in this case, even if the job is associated with a different batch server, all operations will work normally. In other words, you can get information about the job, navigate to its working directory, kill it, fetch files from the working directory, or delete the working directory, just as you would for a job submitted to your default batch server.

If you run any of these commands with an explicit job ID, and the job is on a different batch server than the default one, you need to **provide the full job ID including the server address**. Here is an example.

Suppose you are working on a Robox desktop, so your default batch server is `robox-pro.ceitec.muni.cz`. You have a job with ID 463242 running on this server. Running `qq info 463242` works as expected — qq automatically expands the numerical ID to its full form `463242.robox-pro.ceitec.muni.cz`.

Now suppose you want to get information about job 326432, which is running on the Sokar cluster. Running `qq info 326432` would look up `326432.robox-pro.ceitec.muni.cz` — which is not what you want, and will either produce an error or, worse, silently return information about the wrong job. To get information about the correct job, you need to provide the full job ID: `326432.sokar-pbs.ncbr.muni.cz`.

## [qq cd](commands/qq_cd.md)

Similarly to the previous commands, you can run `qq cd <full-job-id>` to navigate to the input directory of a job submitted to a different server. As above, you need to provide the full job ID including the server address. Note that the input directory must be accessible under the same path on both the target server and your current machine.

## [qq killall](commands/qq_killall.md)

You can kill all your qq jobs on a specific server by running:

```bash
qq killall --server <server-name>
```

***

> The `--server` option is **ignored** on Karolina and LUMI, where only one batch server is available. You also cannot submit jobs from any of the Metacentrum-family clusters to Karolina and LUMI or vice versa.
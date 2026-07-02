# Job collections

Imagine you have a large number of related jobs to manage — for instance a set of windows of an umbrella sampling simulation, a large number of replicas of some simulation system, or just a set of related systems with slightly different simulation parameters. How do you manage them easily? You might think of array jobs, a concept available in both batch systems that qq is built on (PBS and Slurm). Unfortunately, qq does not support array jobs. This may change in the future, but for now qq focuses on a simpler and more flexible alternative: **job collections**.

A job collection is a group of independent, but *typically* related jobs whose input directories *typically* live near each other in the filesystem. Since qq requires one job per directory, a job collection is essentially a group of directories — for example, a set of subdirectories sharing a common parent.

That said, collections do not *actually* exist in qq. There is no registry, no named group, and no explicit membership to manage. Instead, a collection is whatever set of directories you point qq at. qq operator commands accept multiple directory paths at once, so you can treat any group of jobs as a single unit simply by selecting the right paths — usually with a [glob pattern](https://en.wikipedia.org/wiki/Glob_(programming)).

> [!NOTE]
> This approach is intentionally different from NCBR's Infinity ABS, where job collections are named groups with explicit membership that the user must maintain. While named collections have their advantages, they come with overhead: you have to *remember* to add each job to the collection when submitting it and to remove it when it no longer belongs, and of course you have to name the collections appropriately, so you can make sense of them.
>
> qq takes the opposite stance: job collections are implicit and ephemeral, defined by your filesystem structure and the paths you provide at the command line. You can form any grouping you like, at any time, without any bookkeeping at all.

## Working with job collections

All qq commands operating on jobs accept multiple directory paths, so working with a collection requires nothing beyond a glob pattern.

Imagine you are running a set of simulations in a single parent directory, each in its own subdirectory named `job01`, `job02`, `job03`, and so on.

Submit the entire collection by passing multiple scripts to [`qq submit`](commands/qq_submit.md):

```bash
qq submit job*/script.sh (...)
```

Check the status of all jobs in the collection with [`qq info`](commands/qq_info.md):
```bash
qq info -d job*
```

For large collections, the brief output might be more readable:
```bash
qq info -d job* --brief
```

Terminate the entire collection with [`qq kill`](commands/qq_kill.md):
```bash
qq kill -d job* -y
```

Or target only specific jobs:
```bash
qq kill -d job01 job03 -y
```

> [!TIP]
> The `-y` flag skips the confirmation prompt. Without it, qq asks you to confirm **each** termination individually.

If some jobs fail, respawn them with [`qq respawn`](commands/qq_respawn.md):

```bash
qq respawn -d job*
```

> [!TIP]
> You can provide paths to the full collection. `qq respawn` automatically skips jobs that are queued, running, or completed successfully — only failed or killed jobs are resubmitted.

To clear runtime files for all jobs in the collection, use [`qq clear`](commands/qq_clear.md):

```bash
qq clear -d job*
```

> [!TIP]
> `qq clear` will not remove runtime files for active or successfully finished jobs. You can therefore also safely use it on the entire job collection.

Commands [`qq go`](commands/qq_go.md), [`qq sync`](commands/qq_sync.md), and [`qq wipe`](commands/qq_wipe.md) work the same way and are documented in their respective sections.

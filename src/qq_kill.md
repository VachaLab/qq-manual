# qq kill

Used to kill qq jobs. qq's equivalent of Infinity's `pkill`.

> **Quick comparison with pkill**
> - When prompted to confirm that you really want the job to be killed, `qq kill` requires just pressing a single button ('y' for confirming or anything else for refusing) instead of writing the entire 'yes' and pressing enter.
> - `qq kill --force` will attempt to kill even jobs that the qq registers as finished, failed, or already killed. This can be used to remove stuck (lingering) jobs from the batch system.
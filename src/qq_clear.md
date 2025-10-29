# qq clear

Used to clear qq runtime files from the directory. qq's equivalent of Infinity's `premovertf`.

> **Quick comparison with premovertf**
> - `qq clear` checks whether the qq runtime files correspond to an active or successfully completed qq job. If they do **not**, the files are deleted without asking for a confirmation. Otherwise, you get an error and are asked to rerun the command as `qq clear --force`. In contrast, `premovertf` just collects the files and always asks for confirmation before deleting them (unless run as `premovertf -f`).
# Installation

qq is installed with a single command that downloads a pre-built version of qq and sets it up in all home directories of the cluster. The command for each cluster is on its own page: [Robox](robox.md), [Sokar](sokar.md), [Metacentrum Grid](metacentrum.md), [Karolina](karolina.md), and [LUMI](lumi.md). If your computer is not part of any of these clusters, or if you want to build qq yourself, see [Installing manually](manual.md).

## What the installer does

The installer places qq into the `.qq` directory in your home directory and adds a short block marked `This block is managed by qq` to the top of your `.bashrc` and `.profile` (creating them if needed), and of your `.bash_profile` or `.bash_login` if you have them. qq is then available in your terminal, in commands run over SSH, and in your jobs. If you use zsh or fish, see [Using other shells](other_shells.md).

The installer then checks that qq works in every home directory and prints a summary. If a check fails, please [report the issue](https://github.com/VachaLab/qq/issues) with the installer's output. Home directories that are currently unavailable are skipped with a warning; rerun the installer later to set them up.

To start using qq, open a new terminal.

## Updating qq

To **reinstall** or **update** qq, run the installation command for the given cluster again. If you installed qq with any options (like `--zsh`), use them again.

> [!NOTE]
> Updating qq is *usually* safe, even if you have running qq jobs on the cluster. Jobs that are already running will continue using the old version of qq. Loop jobs will automatically switch to the updated version in their next cycle.

qq versions 0.13 and older were installed into the `qq` directory in your home directory. The installer removes the old installation once the new one works. If the `qq` directory in your home contains something other than qq, the installer leaves it in place and prints a warning.

## Installing a specific version

The installation commands always install the latest stable version of qq. To install a different version, replace `latest/download` in the command with `download/` followed by the version's tag. For example, to install qq 0.14.0 on Robox, run:

```bash
curl -fsSL https://github.com/VachaLab/qq/releases/download/v0.14.0/qq-robox-install.sh | bash
```

This works in the same way with the installers for all clusters, with the manual installer, and with the `--zsh` and `--fish` options. All available versions and their tags are listed on the [releases page](https://github.com/VachaLab/qq/releases).

Pre-release versions, which have tags like `v0.15.0.dev.1`, are never installed by the default commands. Install them in the same way, using their tag.

> [!WARNING]
> Pre-release versions are meant for testing new features and may be unstable. Don't use them for important jobs. To return to the stable version, run the normal installation command again.

> [!CAUTION]
> qq 0.13 and older used a different installer. To install one of these versions, first [uninstall](uninstallation.md) your current qq, then use the installation command from the manual for that version.

***

To remove qq, see [Uninstalling qq](uninstallation.md).

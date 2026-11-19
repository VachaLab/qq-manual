# Uninstalling qq

Every cluster has an uninstaller which removes qq and all changes made by the installer. Run it on the same computer where you would run the installer:

```bash
curl -fsSL https://github.com/VachaLab/qq/releases/latest/download/qq-CLUSTER-uninstall.sh | bash
```

Replace `qq-CLUSTER-uninstall.sh` with the uninstaller for your cluster:

| Cluster | Uninstaller |
|---|---|
| Robox | `qq-robox-uninstall.sh` |
| Sokar | `qq-sokar-uninstall.sh` |
| Metacentrum | `qq-metacentrum-uninstall.sh` |
| Karolina | `qq-karolina-uninstall.sh` |
| LUMI | `qq-lumi-uninstall.sh` |
| Installed manually | `qq-uninstall.sh` |

> [!WARNING]
> Jobs need qq on the computing nodes. Make sure you have no queued or running qq jobs on the cluster before uninstalling qq.

The uninstaller removes the `.qq` directory, the fish configuration file `.config/fish/conf.d/qq.fish`, and the blocks marked `This block is managed by qq` from your shell configuration files, deleting the files the installer created. This covers all shells regardless of the installation options, as well as installations of qq 0.13 and older. Home directories that are currently unavailable are skipped with a warning; rerun the uninstaller later to clean them up.

On Robox, the uninstaller removes qq from your desktop and from the computing nodes. If you installed qq on other desktops, run the `qq-uninstall.sh` uninstaller on each of them.

To finish the uninstallation, open a new terminal.

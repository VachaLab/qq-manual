# Using other shells

The installers always set up qq for bash, because qq jobs run in bash. If you use zsh or fish as your working terminal, add the `--zsh` or `--fish` option to the installation command. For example, to set up qq for both zsh and fish on Robox, run:

```bash
curl -fsSL https://github.com/VachaLab/qq/releases/latest/download/qq-robox-install.sh | bash -s -- --zsh --fish
```

The options work the same way with the installers for all clusters. The installer does not change your default shell. When updating qq, use the same options again.

After setting up qq, the installer also checks that qq works in the shells you selected. If zsh or fish is not available on the computer where you run the installer, its check is skipped.

## zsh

```bash
curl -fsSL https://github.com/VachaLab/qq/releases/latest/download/qq-CLUSTER-install.sh | bash -s -- --zsh
```

For zsh, the installer adds a block marked `This block is managed by qq` to the end of your `.zshenv` and `.zshrc`, creating these files if they do not exist. The block in `.zshenv` makes qq available in every zsh, including commands run over SSH. The block in `.zshrc` sets up `qq cd` and tab completion in your terminal and is placed at the end so that it runs after your own completion setup.

## fish

```bash
curl -fsSL https://github.com/VachaLab/qq/releases/latest/download/qq-CLUSTER-install.sh | bash -s -- --fish
```

For fish, the installer creates the file `.config/fish/conf.d/qq.fish` in your home directory. fish loads it in every shell.

## Other shells

Other shells are not supported by the installers. To use qq in another shell, add the directory `.qq/qq` in your home directory to your `PATH`. On Metacentrum, qq is installed in your home directory on `brno12-cerit`, so add `/storage/brno12-cerit/home/YOUR_USERNAME/.qq/qq` instead. The `qq cd` command and tab completion will not work in such a shell.

# Installing on robox

To install qq on the robox cluster (computers of the RoVa Lab), log in to your desktop and run:

```bash
curl -fsSL https://github.com/Ladme/qq/releases/latest/download/qq-robox-install.sh | bash
```

This command downloads the latest version of qq, installs it to your home directory on your desktop, and then installs it to the home directory of the computing nodes.

To finish the installation, either open a new terminal or source your `.bashrc` file.

> **Note:** This does **not** install qq on other desktops with separate local home directories.  
> If you want to use qq on other desktops, you'll need to [install it there separately](install_general.md).  
> (Not installing it there however helps prevent accidentally running jobs on someone else's desktop.)
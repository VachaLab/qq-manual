# Installing on Robox

To install qq on the Robox cluster (computers of the RoVa Lab), log in to your desktop and run:

```bash
curl -fsSL https://github.com/VachaLab/qq/releases/latest/download/qq-robox-install.sh | bash
```

This installs qq on your desktop and in your home directory on the computing nodes. Open a new terminal to start using qq.

> [!NOTE]
> This does **not** install qq on other desktops with separate local home directories.  
> If you want to use qq on other desktops, you'll need to [install it there separately](manual.md#installing-a-pre-built-version).  
> (Not installing it there however helps prevent accidentally running jobs on someone else's desktop.)

***

For more details about the Robox cluster, see [Robox cluster specifics](../specifics/metacentrum_family.md).

# Installing on Metacentrum

To install qq on Metacentrum, log in to any Metacentrum frontend and run:

```bash
curl -fsSL https://github.com/Ladme/qq/releases/latest/download/qq-metacentrum-install.sh | bash
```

This command downloads the latest version of qq and installs it into your home directory on `brno12-cerit`. The script then adds qq's location on this storage to your `PATH` across all Metacentrum machines.

Because qq runs significantly slower when stored on non-local storage, the installation script also configures the `.bashrc` files of all Metacentrum machines to automatically copy qq from `brno12-cerit` to their local scratch space on login. This improves the responsiveness of qq operations.

To complete the installation, either open a new terminal or source your `.bashrc` file.

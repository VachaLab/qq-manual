# Installing on Metacentrum

To install qq on Metacentrum Grid, log in to any [Metacentrum frontend](https://docs.metacentrum.cz/en/docs/computing/infrastructure/frontends) and run:

```bash
curl -fsSL https://github.com/VachaLab/qq/releases/latest/download/qq-metacentrum-install.sh | bash
```

This installs qq into your home directory on `brno12-cerit` and sets up your home directories on all other Metacentrum storages to use it from there. Because qq runs significantly slower from network storage and the Metacentrum frontends are slow, each machine copies qq to its local scratch when you log in or when a job starts. The installer also installs qq on `samson`, which has its own home directory.

Open a new terminal to start using qq.

***

For more details about the Metacentrum Grid, see [Metacentrum Grid specifics](../specifics/metacentrum_family.md).

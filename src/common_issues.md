# Common issues

> [!TIP]
> If something does not work or behaves unexpectedly, it's **never** your fault — it's either a bug or an unclear documentation. [Report it!](https://github.com/VachaLab/qq/issues)

Here are some issues that you may encounter when installing or using `qq`.

## Submitted jobs fail on a node (with an error)

You submit a job, it starts being executed, and then it finishes way too quickly. `qq info` says that the job is in an inconsistent state, and your `.qqout` file contains the following output:

```
/usr/bin/env: 'qq': No such file or directory
```

This indicates that `qq` is not available on the computing node where the job was executed. Any of the following things might have gone wrong:

1. **`qq` has not been installed on the concerned computing node at all.**

This may be especially common on the Robox cluster if you run the job on someone else's desktop. When [installing `qq` on Robox](installation/robox.md), it is installed only on your desktop and on the computing nodes, not on other people's desktops. This is a feature, not a bug — you probably should not run jobs on other people's desktops. If you need to, you can [install qq on their desktop manually](installation/manual.md).

It can also happen if the home directory used by the node was not available when you installed qq. The installer skips such home directories with a warning. Rerun the installation command for the cluster once the home directory is available again.

2. **`qq` has been installed, but the shell on the node does not load it.**

Connect to the node where your job was executed and run:

```bash
bash -lc 'qq --version'
```

If this prints the version of qq, qq is set up correctly on the node and the job failed for a different reason. Otherwise, check that `${HOME}/.qq/env.bash` exists and that your `.bashrc` and `.profile` (and `.bash_profile` or `.bash_login`, if you have them) start with a block like this:

```bash
# >>> This block is managed by qq >>>
if [ -f "$HOME/.qq/env.bash" ]; then . "$HOME/.qq/env.bash"; fi
# <<< This block is managed by qq <<<
```

If the block or the file is missing, rerun the installation command for the cluster. The installer also checks that qq works in the home directory and tells you if it does not.

If everything is in place but qq is still not found, something in your shell configuration files probably overwrites the `PATH` after qq is loaded. Look for a line like `export PATH=/some/directory` which does not include `$PATH` and change it to `export PATH="/some/directory:$PATH"`. If that does not help, [open a GitHub issue](https://github.com/VachaLab/qq/issues).

## Submitted jobs fail on a node without an error

You submit a job, it starts being executed, and then it finishes way too quickly. `qq info` says that the job is in an inconsistent state, and your `.qqout` file contains literally nothing. According to the batch system (or the output of `qq jobs`), the job has finished successfully.

This indicates that qq on the computing node is somehow corrupted. This might have happened during the installation or later.

The simplest solution is to do the following:

1. Connect to the node where your job was being executed.

2. Delete the directory where qq is installed (`rm -rf ${HOME}/.qq/qq`). On Metacentrum, qq is installed only in your home directory on `brno12-cerit`, so delete `/storage/brno12-cerit/home/${USER}/.qq/qq` and also the copy of qq on the local scratch of the node (`rm -rf /scratch/${USER}/qq`).

3. Back on your desktop, rerun the installation command for the cluster.

## PBS GSS error - No credentials were supplied

On Robox, Sokar, or Metacentrum clusters, you may get the following error when running `qq jobs`, `qq stat`, `qq nodes`, or `qq queues`:

```text
ERROR Could not retrieve information about jobs: pbs_gss_establish_context: GSS - gss_acquire_cred: No credentials were supplied, or the credentials were unavailable or inaccessible.
pbs_gss_establish_context: GSS - gss_acquire_cred: unknown mech-code 0 for mech unknown
auth: error returned: 15010
auth: auth_process_handshake_data failure
Permission denied
```

This indicates that your Kerberos ticket has expired. Run `kinit` and provide your password when prompted to generate a new Kerberos ticket. Then rerun the qq command.

## sbatch error: AssocMaxSubmitJobLimit

On Karolina and LUMI, you may get the following error when submitting a job:

```text
ERROR    Failed to submit script '<script_name>': sbatch: error: AssocMaxSubmitJobLimit
sbatch: error: Batch job submission failed: Job violates accounting/QOS policy (job submit limit, user's size and/or time limits).
```

This usually indicates that you did not provide the required `--account` option. Provide it along with your project ID (something like `OPEN-AB-CD` on Karolina or `project_123456` on LUMI; check the output of `it4ifree` or `lumi-allocations`, respectively).

In case you did provide the `--account` option, you are probably running too many jobs on a given queue, you have used all the resources allocated for your project, the specified walltime for your job is too long, or you are asking for too many resources.

## Resubmission fails with `bash: sbatch: command not found`

On Karolina or LUMI, you may encounter the following error when one cycle of a loop job finishes and the next cycle is being submitted:

```
Failed to submit script '<script-name>': bash: sbatch: command not found.
```

This indicates that Slurm commands are not available on the compute node where the job is running. The problem usually lies in your `~/.bashrc` configuration.

If your `.bashrc` contains the following line

```bash
[ -z "$PS1" ] && return
```

then the `[ -z "$PS1" ] && return` condition prevents Slurm commands from becoming available in non-interactive shells.

To fix the issue, either remove the condition or place

```bash
if [ -f /etc/bashrc ]; then
    . /etc/bashrc
fi
```

**before** the condition.

---

## I have some other issue

Open a [GitHub issue](https://github.com/VachaLab/qq/issues) or write an e-mail to `ladmeb@gmail.com`.

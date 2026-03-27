# Common issues

> If something does not work or behaves unexpectedly, it's **never** your fault — it's either a bug or an unclear documentation.

Here are some issues that you may encounter when installing or using `qq`.

## Submitted jobs fail on a node
You submit a job, it starts being executed, and then it finishes way too quickly. `qq info` says that the job is in an inconsistent state, and your `.qqout` file contains the following output:

```
/usr/bin/env: 'qq': No such file or directory
```

This indicates that `qq` is not available on the computing node where the job was executed. Any of the following things might have gone wrong:

1) **`qq` has not been installed on the concerned computing node at all.**

This may be especially common on the Robox cluster if you run the job on someone else's desktop. When [installing `qq` on Robox](install_robox.md), it is installed only on your desktop and on the computing nodes, not on other people's desktops. This is a feature, not a bug — you probably should not run jobs on other people's desktops. If you need to, you can rerun the installation command on their desktop.

2) **`qq` has been installed, but the RC file (`.bashrc`, typically) has not been properly modified.**

Connect to the node where your job was executed and check the contents of `${HOME}/.bashrc`.

The file should contain a block similar to this:

```bash
# >>> This block is managed by qq >>>
# This makes qq available for you on any computer using this directory as its HOME.
if [[ ":$PATH:" != *":/home/ladme/qq:"* ]]; then
    export PATH="$PATH:/home/ladme/qq"
fi
# This makes the qq cd command work.
qq() {
    if [[ "$1" == "cd" ]]; then
        for arg in "$@"; do
            if [[ "$arg" == "--help" || "$arg" == "-h" ]]; then
                command qq "$@"
                return
            fi
        done
        target_dir="$(command qq cd "${@:2}")"
        cd "$target_dir" || return
    else
        command qq "$@"
    fi
}
# This makes qq autocomplete work.
eval "$(_QQ_COMPLETE=bash_source qq)"
# <<< This block is managed by qq <<<
```

If this block is not in the `.bashrc` file, first try reinstalling `qq` on the cluster. If that does not help, [open a GitHub issue](https://github.com/VachaLab/qq/issues).

3) **`qq` has been installed, `.bashrc` has been modified, but it is not read before executing the job.**

This may indicate that when a job was run on the affected node, a login shell was opened instead of the typically used non-login shell. In such cases, the `.bashrc` file may not be read; instead, either the `.profile` or `.bash_profile` file will be read. We need to force the shell to read the `.bashrc` file. Connect to the affected **computing node**, go to your HOME directory (`cd ~`), and add the following to both `.profile` and `.bash_profile` located there:

```bash
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
```

> Note that if `.profile` and `.bash_profile` do not exist, `qq` should create them with the above content during installation. However, if you already have these files, `qq` does not modify them and assumes you have already configured them.


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

***

## I have some other issue
Open a [GitHub issue](https://github.com/VachaLab/qq/issues) or write an e-mail to `ladmeb@gmail.com`.
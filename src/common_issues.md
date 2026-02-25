# Common issues

> If something does not work or behaves unexpectedly, it's **never** your fault — it's either a bug or an unclear documentation.

Here are some issues that you may encounter when installing or using `qq`.

## Submitted jobs fail on a node
You submit a job, it starts being executed, and then it finishes way to quickly. `qq info` says that the job is in an inconsistent state, and your `.qqout` file contains the following output:

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

This may indicate that when a job was run on the affected node, a login shell was opened instead of the typically used non-login shell. In such cases, the `.bashrc` file may not be read; instead, either the `.profile` or `.bash_profile` file will be read. We need to force the shell to read the `.bashrc` file. Add the following to both `.profile` and `.bash_profile`:

```bash
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
```

> Note that if `.profile` and `.bash_profile` do not exist, `qq` should create them with the above content during installation. However, if you already have these files, `qq` does not modify them and assumes you have already configured them.

***

## I have some other issue
Open a [GitHub issue](https://github.com/VachaLab/qq/issues) or write an e-mail to `ladmeb@gmail.com`.
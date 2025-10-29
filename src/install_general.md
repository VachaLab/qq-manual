# Installing manually

## Installing a pre-built version

To install a pre-built version of qq on a single computer or on several computers sharing the same home directory, run:

```bash
curl -fsSL https://github.com/Ladme/qq/releases/latest/download/qq-install.sh | \
bash -s -- $HOME https://github.com/Ladme/qq/releases/latest/download/qq-release.tar.gz
```

To finish the installation, either open a new terminal or source your `.bashrc` file.

## Installing a pre-built version for other shells

If you're not using bash, you'll need to modify the `qq-install.sh` script.

First, download it:
```bash
curl -OL https://github.com/Ladme/qq/releases/latest/download/qq-install.sh
```

Then edit this line to match your shell's RC file:
```bash
BASHRC="${TARGET_HOME}/.bashrc"
# For example, if you use zsh:
BASHRC="${TARGET_HOME}/.zshrc"
```

Next, make the script executable and run it:
```bash
chmod u+x qq-install.sh
./qq-install.sh $HOME https://github.com/Ladme/qq/releases/latest/download/qq-release.tar.gz
```


## Building qq from source
To build and install qq yourself, you'll need `git` and [`uv`](https://docs.astral.sh/uv/getting-started/installation/) installed.

First, clone the qq repository:
```bash
git clone git@github.com:Ladme/qq.git
```

Then navigate to the project directory and install the dependencies:
```bash
cd qq
uv sync --all-groups
```

Build the package using PyInstaller:
```bash
uv run pyinstaller qq.spec
```

PyInstaller will create a directory named `qq` inside `dist`. Copy that directory wherever you want and add it to your PATH.

If you want the `qq cd` command to work, add the following shell function to your shell's RC file:
```bash
qq() {
    if [[ "$1" == "cd" ]]; then
        for arg in "$@"; do
            if [[ "$arg" == "--help" ]]; then
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
```
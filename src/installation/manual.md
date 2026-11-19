# Installing manually

## Installing a pre-built version

To install a pre-built version of qq on a single computer or on several computers sharing the same home directory, run:

```bash
curl -fsSL https://github.com/VachaLab/qq/releases/latest/download/qq-install.sh | bash
```

This installs qq into your current home directory in the same way as the installers for the individual clusters. See [Installation](installation.md) for what the installer does. The pre-built version of qq runs on 64-bit x86 Linux with glibc 2.28 or newer.

Open a new terminal to start using qq.

## Building qq from source

To build qq yourself, you'll need `git` and [`uv`](https://docs.astral.sh/uv/getting-started/installation/) installed.

First, clone the qq repository:

```bash
git clone https://github.com/VachaLab/qq.git
```

Then navigate to the project directory and install the dependencies:

```bash
cd qq
uv sync --all-groups
```

This builds the development version of qq. To build a released version instead, switch to its tag (for example `git checkout v0.14.0`) before installing the dependencies.

Build qq using PyInstaller:

```bash
uv run pyinstaller qq.spec
```

PyInstaller creates a directory named `qq` inside `dist`. To install it, package it in the same way as a qq release, create the installer for it, and run the installer with `QQ_RELEASE_BASE_URL` pointing to your package:

```bash
VERSION="$(dist/qq/qq --version)"
echo "$VERSION" > dist/qq/VERSION
tar -czf dist/qq-release.tar.gz -C dist qq
uv run python scripts/installation/build.py "$VERSION" --output dist/installation
QQ_RELEASE_BASE_URL="file://$PWD/dist" bash dist/installation/qq-install.sh
```

This installs your build into your current home directory.

Open a new terminal to start using qq.

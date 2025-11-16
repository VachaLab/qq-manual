# qq shebang

The `qq shebang` command is a utility for converting regular scripts into qq-compatible scripts. It has no direct equivalent in Infinity.

### Description

Adds the qq run shebang to a script, or replaces an existing one. If no script is specified, it simply prints the qq run shebang to standard output.

```bash
qq shebang [OPTIONS] SCRIPT
```

**SCRIPT** — Path to the script to modify. This argument is optional.

### Examples

Suppose we have a script named `run_script.sh` with the following content:

```bash
#!/bin/bash

# activate the Gromacs module
metamodule add gromacs/2024.3-cuda

# prepare a TPR file
gmx_mpi grompp -f md.mdp -c eq.gro -t eq.cpt -n index.ndx -p system.top -o md.tpr

# run the simulation using 8 OpenMP threads
gmx_mpi mdrun -deffnm md -ntomp 8 -v
```

This script cannot be submitted using `qq submit` because it lacks the qq run shebang.

By running:

```bash
qq shebang run_script.sh
```

the existing bash shebang is replaced with the qq run shebang, resulting in:

```bash
#!/usr/bin/env -S qq run

# activate the Gromacs module
metamodule add gromacs/2024.3-cuda

# prepare a TPR file
gmx_mpi grompp -f md.mdp -c eq.gro -t eq.cpt -n index.ndx -p system.top -o md.tpr

# run the simulation using 8 OpenMP threads
gmx_mpi mdrun -deffnm md -ntomp 8 -v
```

If you run `qq shebang` without specifying a script (you use just `qq shebang`), it simply prints the qq shebang to standard output:

```bash
#!/usr/bin/env -S qq run
```
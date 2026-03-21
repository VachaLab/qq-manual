# Amount of memory (RAM)

Each compute node has a fixed amount of RAM. You can specify how much memory your job needs either per CPU core, per node, or in total:

- `--mem-per-cpu` — specifies the amount of memory per requested CPU core.
- `--mem-per-node` — specifies the amount of memory per requested compute node. Overrides `--mem-per-cpu`.
- `--mem` — specifies the total amount of memory for the entire job. Overrides both `--mem-per-cpu` and `--mem-per-node`.

Memory sizes are specified as `N<unit>` where unit is one of `b`, `kb`, `mb`, `gb`, `tb`, `pb` (e.g., `500mb`, `32gb`).
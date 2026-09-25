# Commands

qq provides a range of commands for submitting, executing, monitoring, and managing your jobs, as well as for displaying information about available compute nodes and submission queues. This section describes how to use each of them.

Each command is run in the terminal using the following syntax:  
`qq [COMMAND] [ARGS] [OPTIONS]`

For example:  
`qq info 123456 -b`  
prints a brief summary of the job with ID `123456`.

To see a list of all available qq commands, simply type:  
`qq`

For detailed information about a specific command, use:  
`qq [COMMAND] --help`

> [!TIP]
> Apart from the core qq commands, there are also **qq scripts** that you can download and use to extend what qq can do. You can find the scripts [here](https://github.com/VachaLab/qq/tree/main/scripts/qq_scripts) and their documentation [here](../scripting.md#official-qq-scripts).

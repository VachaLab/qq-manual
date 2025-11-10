# Working directories

A *working directory* is the directory where a qq job is actually executed. qq copies the data from the input directory to the working directory, executes the submitted script there, and then copies the data back.

Typically, the working directory resides on a compute node’s local storage, but it can also be on a shared filesystem — or even be the same as the input directory.

How the working directory is created depends on the batch system and the specific environment.

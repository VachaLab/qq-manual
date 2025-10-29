# qq submit

Used to submit qq jobs to the batch system. qq's equivalent of Infinity's `psubmit`.

> **Quick comparison with psubmit**
> - `qq submit` does not ask for confirmation. It behaves like `psubmit (...) -y`.
> - Options and parameters for `qq submit` are specified differently. The only positional argument is the script name, everything else is an option. You can see all supported options using `qq submit --help`.
>
>     Infinity: 
>    ```bash
>    psubmit cpu run_script ncpus=8,walltime=12h,props=cl_zero -y
>    ```
>
>    qq: 
>    ```bash
>    qq submit -q cpu run_script --ncpus=8 --walltime=12h --props=cl_zero
>    ```
>
> - Options can also be specified in the submitted script itself or as a combination of command-line and in-script specification. Options specified on the command line take precedence.
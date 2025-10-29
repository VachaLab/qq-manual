# Standard job

Standard job is the default qq job type. Here we describe the entire lifetime of a standard qq job.

## 1. Submitting the job

Submission of a qq job happens using [`qq submit`](qq_submit.md). `qq submit` submits the job to the batch system and generates a qq info file containing metadata and information about the job. This info file is named based on the submitted script, has a file extension `.qqinfo` and is located in the job directory (which is often also called submission or input directory).

The responsibility of handling the job is now given to the batch system which finds a suitable place and time to execute it. You, as a user, do not need to do anything, only wait for the job to be executed.

## 2. Preparing working directory

Once the batch system finds a suitable machine to run the job on, the [`qq run`](qq_run.md) environment takes over. It first sets up a working directory for executing the job on the execution machine. 

In case you requested to run the job in the job directory (by submitting with `--workdir=jobdir`), the job directory is used as a working directory and no further action is needed. In case you requested the job to run on scratch (the default option for PBS) a working directory is created inside the allocated portion of scratch and **all files and directories in the job directory** are copied to the working directory with the exception of the qq info file and the "archive" directory (which we will discuss later, in the section addressing loop jobs) which are not copied. During submitting, you can also specify a list of files which you explicitely do **not** want to be copied to the working directory.

Once the working directory is prepared, the qq info file is modified to set the state of the job as "running". Only then is the actual submitted script actually executed.

## 3. Executing the script

Your submitted script is finally executed using `bash`. It should exit with a non-zero code if everything proceeded successfully or a non-zero code indicating an error. The exit code is propagated to the qq and qq eventually sets the appropriate job state based on it (exit code of 0 corresponds to the "finished" state, anything else to the "failed" state).

Standard output from your script is stored into a file with the name of your script and a file extension `.out`. The standard error output from your script is stored into a similar file with a file extension `.err`.

## 4. Finalizing the execution

After the submitted script is executed, qq performs a clean-up. 

If your job is running in the job directory (`--workdir=jobdir`), the clean up is simple: the state of the job ("finished" or "failed") is marked to the qq info file associated with the job and the execution finishes.

If your job is running on scratch, the performed clean up depends on whether your script was executed successfully or not. If it was, all files from the working directory are transferred to the job directory and the entire working directory, or at least its content, is deleted. Finally, qq sets the job sttate to "finished". If the execution of your script failed, no files are transferred and remain on the execution machine in the working directory for further inspection (you can navigate to the working directory of your job usig [`qq go`](qq_go.md)). The state of the job is then set to "failed".

No matter whether the job finished successfully or failed, a qq output file (named based on your script with a file extension `.qqout`) is generated in the job directory. This file contains basic information about what qq was doing and when when executing your job. It may take a few seconds for the batch system to write this file to the job directory, so if it does not appear immediately, be patient.

## Killing a qq job

In case your job is killed (either by using [`qq kill`](qq_kill.md)) or by the batch system itself (e.g. because your job exceeded the allocated walltime), all the files remain in the working directory on the execution machine. The only thing that qq performs when a job is killed is terminating the execution of your submitted script and setting the job state to "killed".

## Additional notes
- Almost all operations while setting up the working directory or finalizing the job execution are retried in case of an error. This is to avoid the entire job crashing in case a shared storage, server or a machine temporarily becomes unavalable. If a qq operation fails, qq waits for several minutes and then tries to repeat the operation. A single operation may be attempted up to 3 times. If the operation fails 3 times, qq terminates and reports an error. qq does not retry the execution of your script.
- In case your job fails with an exit code of 90-99, this usually indicates that a specific qq operation failed. Check the qq output file (file with the `.qqout` file extension) for more details. An exit code of 99 indicates a critical, unexpected error. This usually indicates a bug in the qq environment. Please report such situations.
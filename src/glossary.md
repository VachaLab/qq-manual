# Glossary

- **compute node** – Computer where a job can be executed.

- **continuous job** – Simple alternative to a loop job. Job which submits its continuation right before finishing, while not performing any other advanced operations [[read more](job_types/continuous_job.md)].

- **input directory** – Directory from which the job is submitted. Contains the qq info file.

- **job directory** – See "input directory".

- **loop job** – Job which submits its continuation right before finishing, archives files and counts cycles [[read more](job_types/loop_job.md)].

- **main working node** – Working node (see below) responsible for managing a job.

- **qq info file** – YAML-formatted file containing information about a qq job. Necessary for performing operations with the qq job. Located in the input directory.

- **qq job** – Job of the batch system submitted using [`qq submit`](commands/qq_submit.md).

- **standard job** – Default qq job [[read more](job_types/standard_job.md)].

- **submission directory** – See "input directory".

- **work(ing) directory** – Directory where the job is being executed [[read more](resources/work_dir.md)].

- **working node** – Compute node where the job is being (or has been) executed.
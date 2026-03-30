# Job types

qq currently supports three job types: `standard`, `loop`, `continuous`.

`standard` jobs are the default type. Any job for which you don't specify a `job-type` when submitting is considered `standard`. Read more about standard jobs [here](standard_job.md).

`loop` jobs automatically submit their continuation before finishing. They also track their cycle and archive output files. Read more about them [here](loop_job.md).

`continuous` jobs are "poor man's loop jobs". Similarly to `loop` jobs, they automatically submit their continuation before finishing, but they do not track their cycle and do not perform any archiving operations. Read more about them [here](continuous_job.md).
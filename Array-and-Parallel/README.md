# Array Jobs and Parallel Tasks

# About
Figuring out effective ways to submit large numbers of jobs can be frustrating. The script examples below are designed to help implement different techniques to run jobs as arrays or in parallel

Each script is designed to print commands and information specific to that job for better visualization/demonstration purposes.

HPC users should be able to run each script with minor modifications (i.e. adding their PI's group name) to experiment.


# Array Jobs
> ⚠️ Be careful using custom output filenames (e.g. ```-o myjob.out```) in array jobs. If the filename specified doesn't include unique descriptors (e.g., array ID), each subjob will write to and replace that filename, meaning you'll only capture the output from a single subjob. See our [documentation on filename patterns](https://public.confluence.arizona.edu/display/UAHPC/Running+Jobs+with+SLURM#RunningJobswithSLURM-SLURMOutputFilenamePatterns) for help. 

An array job is a way to easily submit multiple jobs using the same SLURM script with a single ```sbatch```. When an array job is submitted, it will create multiple subjobs, each using the same SLURM directives and same commands as written in the main script.

### [Basic Array Job](Basic-Array-Job)
If you have never used array jobs before, start here.
Submit multiple jobs using the same script with only one ```sbatch``` command.

### [Array Jobs With Text Filenames](Array-Read-Filenames)
Run multiple jobs where each opens a different file but the naming scheme isn't conducive to automating the process using simple array indices as shown in Basic_Array_Job (i.e. 1.txt, 2.txt,...).

### [Sample Array With Input Parameters](Array-Read-Parameters)
Run multiple jobs where each uses a distinct combination of input parameters. Very similar to reading text filenames above. 

## Software-Specific Array Examples
### [Array Job in R](/R-Examples/R-Array-Jobs/)
Run multiple simulations using the same R script and the same batch submission script while saving the output to unique filenames. Redirects to R examples.

--------

# Parallel Jobs

### [Basic GNU Parallel Job](Basic-Parallel-Job)
Run multiple tasks on a single node without using an array job. Tasks that are not run immediately due to space restrictions are queued and are automatically executed as space becomes available. The parallelization was accomplished using [GNU Parallel](https://www.gnu.org/software/parallel/).

### [Parallel Processing in Python with Multiprocessing](https://ua-researchcomputing-hpc.github.io/Python-Examples/Multiprocessing/)
The python multiprocessing package is a popular way to spread a workflow over multiple CPUs. This example demonstrates how to assign multiple CPUs to a SLURM job and then utilize them in a multiprocessing python job.

---------

# Combining Parallel and Array Jobs

### [Array Job With GNU Parallel](Array-and-Parallel)
This script is roughly an extension of **Sample Array Job**, but with the additional step of parallelizing tasks within each subjob. The parallelization was accomplished using [GNU Parallel](https://www.gnu.org/software/parallel/). This allows for running a tremendous number of tasks.

*****

[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)

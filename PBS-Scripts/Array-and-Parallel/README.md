# Read Me

## About
Figuring out effective ways to submit large numbers of jobs can be frustrating. This repository is designed to try to help implement different techniques to run jobs as arrays/in parallel. They are written for submission with PBS scheduling software on University of Arizona's HPC. 

As a note, these scripts have typically been written in response to submitted user questions and as such may have some idiosynchracies that are specific to each case (I'm working to iron them out to make each example more uniform). Each script is designed to print commands and information specific to that job for better visualization/demonstration purposes. 

HPC users should be able to run each script with minor modifications (i.e. adding their PI's group name) to experiment.


### What are some problems that arise when submitting lots of jobs?

Common things that can wreak havoc on HPC are submissions that overload our scheduling software. This typically boils down to two things:

1. Submitting individual jobs with for loops    -- Too many jobs/Jobs submitted too quickly
2. Submitting a tremendous number of array jobs -- Too many jobs for the scheduler to keep track of

This can lead to low/nonexistent system performance and unhappy users. This is designed to be an expanding repo of techniques that will run jobs more efficiently without putting as much stress on the system. 




# Scripts

**Script Names are presented in order of type and increasing complexity**

## Array Jobs

An array job is a way to easily submit multiple jobs using the same PBS script with a single ```qsub```. When an array job is submitted, it will create multiple subjobs, each using the same PBS directives and same commands as written in the main script.


### [Basic_Array_Job](Basic_Array_Job.md)

#### Problem

A user wants to submit multiple jobs using the same script with different input.

#### Solution

Using an array job can run multiple tasks and differentiate them using array indices. Only one ```qsub``` command is necessary.


### Sample_Array_Read_Filenames


#### Problem

A user wants to run multiple jobs where each opens a different file to analyze but the naming scheme isn't conducive to automating the process using simple array indices as shown in Basic_Array_Job (i.e. 1.txt, 2.txt, ...).

#### Solution

The user can create an input file with one filename per line. One filename is read in per array job using array indices. 

### Sample_Array_Input_Parameters

#### Problem

A user wants to run multiple jobs where each uses a distinct combination of input parameters. 

#### Solution

Very similar to Sample_Array_Read_Filenames, the user may generate an input file with the relevant parameter combinations and read individual lines into their array jobs using the array indices.





## Parallel Jobs

### Sample_Parallel_Job

#### Problem
A user wants to run multiple tasks on a single node without using an array job

#### Solution
GNU Parallel allows for the user to submit many tasks to a single node for simultaneous execution (~one task/cpu). Tasks that are not run immediately due to space restrictions are queued behind the running jobs and are executed as space becomes available.

## Parallel + Array Jobs

### Sample_Array_with_GNUParallel

#### Problem

A user wants to run more tasks than the PBS scheduler can keep track of (e.g. thousands)

#### Solution

This script is roughly an extension of Sample_Array_Job.pbs, but with the additional step of parallelizing tasks within each subjob. The parallelization was accomplished using GNU Parallel (https://www.gnu.org/software/parallel/)
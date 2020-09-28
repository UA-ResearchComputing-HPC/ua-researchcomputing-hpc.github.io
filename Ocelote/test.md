# Array and Parallel Script Examples

## About
Figuring out effective ways to submit large numbers of jobs can be frustrating. This repository is designed to try to help implement different techniques to run jobs as arrays/in parallel. They are written for submission with PBS scheduling software on University of Arizona's HPC.

Each script is designed to print commands and information specific to that job for better visualization/demonstration purposes. HPC users should be able to run each script with minor modifications (i.e. adding their PI's group name) to experiment.

### What are some problems that arise when submitting lots of jobs?
Common things that can wreak havoc on HPC are submissions that overload our scheduling software. This typically boils down to two things:

1. Submitting individual jobs with for loops -- Too many jobs/Jobs submitted too quickly
2. Submitting a tremendous number of array jobs -- Too many jobs for the scheduler to keep track of

This can lead to low/nonexistent system performance and unhappy users. This is designed to be an expanding repo of techniques that will run jobs more efficiently without putting as much stress on the system.

## Scripts

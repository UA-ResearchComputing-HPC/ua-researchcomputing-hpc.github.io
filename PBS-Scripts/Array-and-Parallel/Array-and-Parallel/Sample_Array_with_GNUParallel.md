# Array Job with GNU Parallel

This script combines the methods of Basic_Array_Job and Basic_Parallel_Job to execute a large number of jobs.

## What problem does this help fix?

Sometimes you need to run a _lot_ of jobs. More than can be reasonably accomplished using arrays since submitting thousands of these can be a problem for the system, and Gnu Parallel is challenging to make work in a multi-node environment. In this case, we can combine the forces of Gnu Parallel and array jobs such that we can distribute a chunk of tasks across multiple nodes where Gnu Parallel will execute them. 

## Script Example

[Click here to download example](Sample_Array_GNUParallel.tar.gz)
```
#!/bin/bash
#PBS -q standard
#PBS -W group_list=<GROUP NAME>
#PBS -j oe
#PBS -N Sample_Array_with_GNUParallel
#PBS -l select=1:ncpus=28:mem=168gb:pcmem=6gb
#PBS -l walltime=00:01:00
#PBS -J 1-2

module load parallel
cd $PBS_O_WORKDIR
BLOCK_SIZE=56
seq $(($PBS_ARRAY_INDEX*$BLOCK_SIZE-$BLOCK_SIZE+1)) $(($PBS_ARRAY_INDEX*$BLOCK_SIZE)) | parallel echo "JOB ID: $PBS_JOBID HOST NODE: $HOSTNAME EXAMPLE COMMAND: ./executable input_{}"
```

## Script Breakdown
Like with the basic parallel example, GNU Parallel is accessible on Ocelote as a module:
```
module load parallel
```

The general goal here for illustration purposes is to set up a "block size." This is the number of tasks Gnu Parallel will be executing in each subjob. 

```
BLOCK_SIZE=56
```
In this case, we're asking for 56 tasks per subjob (an arbitrary choice chosen because it's divisible by 28). Since we're submitting an array job with two subjobs, that totals 112 tasks. The array indices are then used to differentiate tasks. ```seq n m``` generates a sequence of integers from ```n``` to ```m``` (inclusive). We'll use this with some arithmetic below:

```
seq $(($PBS_ARRAY_INDEX*$BLOCK_SIZE-$BLOCK_SIZE+1)) $(($PBS_ARRAY_INDEX*$BLOCK_SIZE))
```

```PBS_ARRAY_INDEX``` is either 1 or 2, depending on the subjob. ```BLOCK_SIZE``` is set by the user. In this case, we've set it to 56. Looking at the arithmetic:

**Subjob 1**:
```seq $(($PBS_ARRAY_INDEX*$BLOCK_SIZE-$BLOCK_SIZE+1)) $(($PBS_ARRAY_INDEX*$BLOCK_SIZE))``` = ```seq 1*56-56+1 1*56``` = ```seq 1 56```

**Subjob 2**:
```seq $(($PBS_ARRAY_INDEX*$BLOCK_SIZE-$BLOCK_SIZE+1)) $(($PBS_ARRAY_INDEX*$BLOCK_SIZE))``` = ```seq 2*56-56+1 2*56``` = ```seq 57 112```

Shown by the above, this is just a way to cleanly partition the integers 1 through 112 into two distinct groups. Each group will be assigned to a node.

## Script Submission Command

```
$ qsub Sample_Array_with_GNUParallel.pbs 
3031256[].head1.cm.cluster
```

## Output Files

```
$ ls *.o*
Sample_Array_with_GNUParallel.o3031256.1
Sample_Array_with_GNUParallel.o3031256.2
```

## File Contents

A total of 112 jobs were run using an array job with two subjobs on two nodes, i4n22 and i4n20. To view the full contents of the output file, download the example above.
```
$ cat *.o*
JOB ID: 3031256[1].head1.cm.cluster HOST NODE: i4n22 EXAMPLE COMMAND: ./executable input_1
JOB ID: 3031256[1].head1.cm.cluster HOST NODE: i4n22 EXAMPLE COMMAND: ./executable input_2
JOB ID: 3031256[1].head1.cm.cluster HOST NODE: i4n22 EXAMPLE COMMAND: ./executable input_3
JOB ID: 3031256[1].head1.cm.cluster HOST NODE: i4n22 EXAMPLE COMMAND: ./executable input_4
JOB ID: 3031256[1].head1.cm.cluster HOST NODE: i4n22 EXAMPLE COMMAND: ./executable input_5
JOB ID: 3031256[1].head1.cm.cluster HOST NODE: i4n22 EXAMPLE COMMAND: ./executable input_6
JOB ID: 3031256[1].head1.cm.cluster HOST NODE: i4n22 EXAMPLE COMMAND: ./executable input_7
...
```

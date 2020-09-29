# Basic Parallel job

This example demonstrates how to parallelize multiple tasks within one job. 

## Script Example
[Click here to download example](Basic_Parallel_Job.tar.gz)

```
#PBS -q standard
#PBS -W group_list=hpcteam
#PBS -j oe

#PBS -N Sample_Parallel_Job

#PBS -l select=1:ncpus=28:mem=168gb:pcmem=6gb
#PBS -l walltime=00:02:00
```

## Script Breakdown

In this case, we'll make use of a full node with Gnu Parallel which is available as a module on Ocelote:

```
module load parallel
```

The meat of the command lies here: 

```
seq 1 100 | parallel 'DATE=$( date +"%T" ) && sleep 0.{} && echo "Host: $HOSTNAME ; Date: $DATE; {}"'
```

```seq 1 100``` Generates a list between 1 and 100 (inclusive). We pipe that into a parallel command which will generate one task per element

GNU Parallel will find the space on our node as it works through the relevant tasks. Everything in ```'...'``` is the command that will be executed

**Inside the ```'...'```:**

```DATE=$( date +"%T" )``` sets DATE so we can visualize the tasks and when they're being executed

```sleep 0.{}``` forces each task to sleep for 0.n seconds, where n is the input integer from the ```seq``` command. This means, for example, the 2nd task will wait longer than the 10th task, as can be seen in the output file. This is used to demonstrate that these tasks are being executed in parallel.

```echo "Host: $HOSTNAME ; Date: $DATE; {}"``` Prints out the relevant information. ```{}``` is the piped input which, in this case, will be an integer between 1 and 100. 

## Script Submission Command
```
$ qsub Sample_Parallel_Job.pbs 
3030688.head1.cm.cluster
```

## Output Files
Since this isn't an array job, there will only be one output file:

```
$ ls *.o*
Sample_Parallel_Job.o3030688
```

## File Contents
For the full file contents, download the example above.
```
$ cat Sample_Parallel_Job.o3030688 
Host: i10n23 ; Date: 15:04:45; 1
Host: i10n23 ; Date: 15:04:45; 10
Host: i10n23 ; Date: 15:04:45; 11
Host: i10n23 ; Date: 15:04:45; 2
Host: i10n23 ; Date: 15:04:45; 12
...
```

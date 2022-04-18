# Recover and Delete ```/tmp``` Files

[![](/Images/Download-Button.png)](Recover-tmp-Files.tar.gz)

# Overview
Sometimes you may want to use a compute node's local storage, accessed as ```/tmp```, as scratch space for your workflows. However, what happens if your job runs over its allocated walltime and is killed? In this case, you may not be able to retrieve your files before your job ends. This is not ideal for you since you may lose important data, and it's not ideal for the system since large unclaimed files may lead to nodes being taken offline for manual cleanup.

One solution might be to request a signal from SLURM ```n``` seconds before the job's walltime is reached. This can be used to trigger a ```trap``` cleanup function that will recover your files to your account while removing them from the scratch space before the job terminates.

# Submission Script
Below is an example submission script that uses the trap method to recover the file it generates before the job is killed. 

First, we request that a signal, ```USR1```, is sent ```n``` seconds before the job reaches its walltime (additional SLURM documentation on SBATCH directives can be found here: [https://slurm.schedmd.com/sbatch.html](https://slurm.schedmd.com/sbatch.html)) 
```
#SBATCH --signal=B:USR1@n
```
In this case, we'll request four minutes of runtime and we'll specify we want our signal sent 30 seconds before that time is reached using:
```
#SBATCH --time=00:04:00
# asks SLURM to send the USR1 signal 30 seconds before end of the time limit
#SBATCH --signal=B:USR1@30
```
Next, we'll define a ```trap``` function called ```cleanup``` used to retrieve and remove our files from scratch. Since we want to retrieve our files either when we get the ```USR1``` signal or when we get the ```EXIT``` signal, we'll define a variable to determine if we've already gone through the cleanup process before so we don't execute it multiple times. Specifically, if a script is told to clean up after receiving the USR1 signal, then exits with the EXIT signal, we don't want to rerun our cleanup. We'll use ```CLEANUP``` to catch this in the following function:
```
cleanup()
{
    if [ -z "$CLEANUP" ] ; then
        export CLEANUP=true
        echo "function 'cleanup' called at $(date)"; echo
        echo "Moving $TMP_PATH/$FILENAME to $SAVE_PATH"
        check_filepath $SAVE_PATH
        [ $? -eq 1 ] && echo "Save filepath doesn't exit. Creating" && mkdir -p $SAVE_PATH
        check_filepath $SAVE_PATH
        cp $TMP_PATH/$FILENAME $SAVE_PATH/$FILENAME; echo
        echo "Deleting $TMP_PATH"
        rm -rf $TMP_PATH ; echo
        echo "Checking"
        check_filepath $TMP_PATH
        exit
    else
        echo "'cleanup' already performed. Exiting"
    fi
}
```
Using ```trap 'cleanup' USR1 EXIT``` will tell our script to execute the cleanup if it receives either the ```USR1``` or ```EXIT``` signal. 

The full script is included below:
```
#!/bin/bash
#SBATCH --job-name=save_files_example
#SBATCH --account=hpcteam
#SBATCH --partition=standard
#SBATCH --ntasks=1
#SBATCH --nodes=1
#SBATCH --time=00:04:00
# asks SLURM to send the USR1 signal 30 seconds before end of the time limit
#SBATCH --signal=B:USR1@30


# File/directory names relevant to this job. The path and file defined with
# TMP_PATH and FILENAME will be created as part of the workflow. To run this
# script, 
SAVE_PATH=$SLURM_SUBMIT_DIR/OUTPUT
TMP_PATH=/tmp/$USER
FILENAME=test.out

# Function to elegantly test the existence of a directory
check_filepath()
{
    [ -d $1 ] && echo "Directory $1 exists." && return 0
    [ ! -d $1 ] && echo "Directory $1 does not exist." && return 1
}

# We define a function used to collect our output files and remove anything
# from /tmp that we've generated. Since trap will be called either if USR1 or
# EXIT is detected, 
cleanup()
{
    if [ -z "$CLEANUP" ] ; then
        export CLEANUP=true
        echo "function 'cleanup' called at $(date)"; echo
        echo "Moving $TMP_PATH/$FILENAME to $SAVE_PATH"
        check_filepath $SAVE_PATH
        [ $? -eq 1 ] && echo "Save filepath doesn't exit. Creating" && mkdir -p $SAVE_PATH
        check_filepath $SAVE_PATH
        cp $TMP_PATH/$FILENAME $SAVE_PATH/$FILENAME; echo
        echo "Deleting $TMP_PATH"
        rm -rf $TMP_PATH ; echo
        echo "Checking"
        check_filepath $TMP_PATH
        exit
    else
        echo "'cleanup' already performed. Exiting"
    fi
}

# "trap" will activate our cleanup function either when the USR1 signal is sent
# when we're 30 seconds out from the job hitting its walltime limit or when 
# the job exits
trap 'cleanup' USR1 EXIT

# We'll create a sample workflow that will eat up more time than we've allocated
# to the job. At some point, slurm will send the USR1 signal and this workflow
# will be interrupted and the cleanup will activate
echo "starting calculation at $(date)"

echo "Making $TMP_PATH"
mkdir -p $TMP_PATH
echo "checking" ; check_filepath $TMP_PATH
echo "Performing calculations" >> $TMP_PATH/$FILENAME
for i in $(seq 1 10) 
do
    echo "Iteration $i: $(date)" >> $TMP_PATH/$FILENAME
    sleep 30
    wait
done

wait
```

# Submitting the Job
```
[netid@junonia ~]$ sbatch save_files.slurm 
Submitted batch job 271237
```

# Output Files
There will be two output files generated by this job. The first is the standard SLURM output that will show up in your submission directory as soon as your job starts running. The contents should show something like:
```
[netid@junonia ~]$ cat slurm-271237.out 
starting calculation at Wed Feb  9 15:44:40 MST 2022
Making /tmp/netid
checking
Directory /tmp/netid exists.
function 'cleanup' called at Wed Feb  9 15:48:10 MST 2022

Moving /tmp/netid/test.out to /xdisk/netid/Recover-tmp-Files/OUTPUT
Directory /xdisk/netid/Recover-tmp-Files/OUTPUT does not exist.
Save filepath doesn't exit. Creating
Directory /xdisk/netid/Recover-tmp-Files/OUTPUT exists.

Deleting /tmp/netid

Checking
Directory /tmp/netid does not exist.
'cleanup' already performed. Exiting
Detailed performance metrics for this job will be available at https://metrics.hpc.arizona.edu/#job_viewer?action=show&realm=SUPREMM&resource_id=4&local_job_id=271237 by 8am on 2022/02/10.
```
The recovered file generated on the scratch storage will also be present in a directory called OUTPUT:
```
[netid@junonia ~]$ cat OUTPUT/test.out 
Performing calculations
Iteration 1: Wed Feb  9 15:44:40 MST 2022
Iteration 2: Wed Feb  9 15:45:10 MST 2022
Iteration 3: Wed Feb  9 15:45:40 MST 2022
Iteration 4: Wed Feb  9 15:46:10 MST 2022
Iteration 5: Wed Feb  9 15:46:40 MST 2022
Iteration 6: Wed Feb  9 15:47:10 MST 2022
Iteration 7: Wed Feb  9 15:47:40 MST 2022
```

*****
[![](/Images/home.png)](https://ua-researchcomputing-hpc.github.io/) 
[![](/Images/back.png)](../)

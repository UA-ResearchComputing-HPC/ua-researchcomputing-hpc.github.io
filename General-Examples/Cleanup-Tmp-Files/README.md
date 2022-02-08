# Cleanup Files



```
#!/bin/bash
#SBATCH --job-name=save_files_example
#SBATCH --account=YOUR_GROUP
#SBATCH --partition=standard
#SBATCH --ntasks=1
#SBATCH --nodes=1
#SBATCH --time=00:04:00
# asks SLURM to send the USR1 signal 30 seconds before end of the time limit
#SBATCH --signal=B:USR1@120


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
# from /tmp that we've generated
cleanup()
{
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
}

# "trap" will activate our cleanup function either when the USR1 signal is sent
# when we're 30 seconds out from the job hitting its walltime limit or when 
# the job exits
trap 'cleanup' USR1
trap 'cleanup' EXIT

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

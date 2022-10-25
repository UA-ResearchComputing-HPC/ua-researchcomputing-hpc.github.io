# SLURM Job Dependencies Example

## Overview

Sometimes projects need to be split up into multiple steps where each is dependent on the completion of the previous step. SLURM dependencies are a way to automate this process. In this example, we'll create a number of three-dimensional plots using Python and will combine them into a gif as the last step. A job dependency is optimal in this case since the job that creates the gif is dependent on all the images being present.

## Data structure
```console
(elgato) [user@wentletrap volcano]$ tree
.
├── create_gif.slurm
├── data
│   └── volcano.csv
├── generate_frames.slurm
├── images
├── output
│   ├── archives
│   └── slurm_files
├── submit-video-job
└── volcano.py
```
# Scripts

## Python script
The Python example script was pulled and modified from the [Python graph gallery](https://www.python-graph-gallery.com/3d/) and the CSV file used to generate the image was pulled from https://raw.githubusercontent.com/holtzy/The-Python-Graph-Gallery/master/static/data/volcano.csv

```python
#!/usr/bin/env python3
from mpl_toolkits.mplot3d import Axes3D
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns
import sys

n = int(sys.argv[1]) # <- We'll import an array index to rotate our image

# Original example from: https://www.python-graph-gallery.com/3d/
# CSV available from   : https://raw.githubusercontent.com/holtzy/The-Python-Graph-Gallery/master/static/data/volcano.csv
data = pd.read_csv("data/volcano.csv")
 
# Transform it to a long format
df=data.unstack().reset_index()
df.columns=["X","Y","Z"]
 
# And transform the old column name in something numeric
df['X']=pd.Categorical(df['X'])
df['X']=df['X'].cat.codes
 
# Make the plot
fig = plt.figure()
ax = fig.add_subplot(projection='3d')
ax.set_axis_off()
ax.plot_trisurf(df['Y'], df['X'], df['Z'], cmap=plt.cm.viridis, linewidth=0.2)
ax.view_init(30, 45 + n)
plt.savefig('images/image%s.png'%n,format='png',transparent=False)
```

## SLURM script to generate frames (```generate_frames.slurm```)


```bash
#!/bin/bash

#SBATCH --account=hpcteam
#SBATCH --partition=standard
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --time=00:05:00
#SBATCH --job-name=generate_frames
#SBATCH -o output/slurm_files/%x-%A.out
#SBATCH -e output/slurm_files/%x-%A.err
#SBATCH --open-mode=append
#SBATCH --array=1-360

python3 volcano.py $SLURM_ARRAY_TASK_ID
```

#SLURM script to combine frames into gif (```create_gif.slurm```)
```bash
#!/bin/bash

#SBATCH --account=hpcteam
#SBATCH --partition=standard
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --time=00:10:00
#SBATCH --job-name=make_video
#SBATCH -o output/slurm_files/%x-%j.out
#SBATCH -e output/slurm_files/%x-%j.err

module load ffmpeg 
ffmpeg -framerate 25 -i $PWD/images/image%d.png -r 30  -b 5000k volcano.mp4
ffmpeg -i volcano.mp4 -loop 0 -vf scale=400:240 volcano.gif
rm volcano.mp4
cd images
DATE_FORMAT=$(date +%m-%d-%Y.%H:%M:%S)
tar czvf volcano-images-${DATE_FORMAT}.tar.gz image*.png
mv *tar.gz ../output/archives
rm -rf ./*.png
```
## Script to automate job submissions

```bash
#!/bin/bash

printf "Submitting job to generate images\n"
jobid=$(sbatch --parsable generate_frames.slurm)

printf "Job submitted with ID: $jobid\n\n"

printf "Submitting job dependency. Combines images into a video file\n"
sbatch --dependency=afterany:$jobid create_gif.slurm 
```

# Submitting the jobs

```console
(elgato) [user@wentletrap volcano]$ bash submit-video-job 
Submitting job to generate images
Job submitted with ID: 447878

Submitting job dependency. Combines images into a video file
Submitted batch job 447879
```

```console
(elgato) [user@wentletrap volcano]$ squeue --user user
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
    447878_[9-360]  standard generate     user PD       0:00      1 (None)
            447879  standard make_vid     user PD       0:00      1 (Dependency)
          447878_1  standard generate     user  R       0:02      1 cpu16
          447878_2  standard generate     user  R       0:02      1 cpu37
          447878_3  standard generate     user  R       0:02      1 cpu37
          447878_4  standard generate     user  R       0:02      1 cpu37
          447878_5  standard generate     user  R       0:02      1 cpu37
          447878_6  standard generate     user  R       0:02      1 cpu37
          447878_7  standard generate     user  R       0:02      1 cpu37
          447878_8  standard generate     user  R       0:02      1 cpu37
```

```console
(elgato) [user@wentletrap volcano]$ tree
.
├── create_gif.slurm
├── data
│   └── volcano.csv
├── generate_frames.slurm
├── images
├── output
│   ├── archives
│   │   └── volcano-images-10-25-2022.12:52:19.tar.gz
│   ├── gifs
│   │   └── volcano.gif
│   └── slurm_files
│       ├── generate_frames-447878.err
│       ├── generate_frames-447878.out
│       ├── make_video-447879.err
│       └── make_video-447879.out
├── submit-video-job
└── volcano.py

6 directories, 11 files
```

# Output
If everything is successful, there should be a gif of a rotating volcano in the directory ```output/gifs/```
~[](volcano.gif)

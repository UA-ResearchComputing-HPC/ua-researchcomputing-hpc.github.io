# SLURM Job Dependencies Example

Sometimes projects need to be split up into multiple jobs where later steps are dependent on the completion of previous jobs. SLURM dependencies are a way to automate this process. In this example, we'll create a number of three-dimensional plots using Python and will combine them into a gif as the last step. A job dependency is optimal in this case since the job that creates the gif is dependent on all the images being present, i.e. each array subjob must be complete.

The Python example script was pulled and modified from the [Python graph gallery](https://www.python-graph-gallery.com/3d/) and the CSV file used to generate the image was pulled from https://raw.githubusercontent.com/holtzy/The-Python-Graph-Gallery/master/static/data/volcano.csv


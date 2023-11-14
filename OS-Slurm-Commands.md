# Slurm

Tutorial websites: https://slurm.schedmd.com/tutorials.html

-------------------------------------------

## Basic Slurm commands

- `man slurm`: manual pages for all Slurm deamons, commands, and API functions

- `sbatch`: submit a job script for later execution. The script will typically contain one or more srun commands to launch parallel tasks
  - `sbatch job.sh`:  submit a job

- `scancel`: cancel a pending or running job or job step. It can also be used to send an arbitrary signal to all processes associated with a running job or job step.
  - `scancel jobid`: cancel a job
  - `scancel -u username`: cancel all jobs under username
  - `scancel -t PD`: cancel all your pending jobs

- `squeue`: reports the states of jobs or job steps. It has a wide variety of filtering, sorting and formatting options. 
  - `squeue -u myuan`: reports the states of jobs under username

- `sinfo`: reports the state of partitions and nodes managed by Slurm. It has a wide variety of filtering, sorting and formatting options.
  - `sinfo --Node --long`: view the details of this cluster

- `sacct`: shows the completed and running job list as a table

- `sbcast`: transfer a file from local disk to local disk on the nodes allocated to a job. This can be used to effectively use diskless computer nodes or provide improved performance relative to a shared file system

- `scontrol`: the administrative tool used to vies and/or modify Slurm state. Not that many scontrol commands can only be excecuted as user root.

- `srun`:
	• used to submit a job for execution or initiate job steps in real time. 
	• has a wide variety of options to specify respurce requirements, including
		○ minimum and maximum node count,
		○ processor count,
		○ specific nodes to use or not use
		○ specific node characteristics (so much memory, disk space, certain required features, etc.)


-------------------------------------------

## An template of a `.sh` file to place (an array of) jobs

The contents are similar to a `.pbs` file:

```
#!/bin/bash -l
#SBATCH --time=1:00:00
#SBATCH --nodes=1
#SBATCH --job-name=test_jobs
#SBATCH --output=out.log
#SBATCH --error=err.log
#SBATCH --workdir=./
#SBATCH --mem=4G
#SBATCH --array=1-4

## Add this line if not adding R/brew to PATH (optional)
PATH=/bin:/gsc/blt/linuxbrew/bin:/usr/bin:$PATH  

## Get Array ID
jobid=${SLURM_ARRAY_TASK_ID}

## Parameter file
param_file="./tests/params.txt"

## Read line #jobid from the parameter file
PARAMETERS=$(sed "${jobid}q;d" ${param_file})
echo "Parameters: ${PARAMETERS}"

Rscript --vanilla ~/path/to/file/mytest.r ${PARAMETERS}
```


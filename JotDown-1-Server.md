# How to Configure an R on the Server from Scratch for a Green Hand

> Ooooops forgot to save the edited document...
> Well, never mind.

Here is my personal experience that help run an Rscript on Sockeye server successfully. 

## Basics

### Login

To connect to Sockeye, Mac users can access via command line with the terminal, and Windows users can use tools such as Putty and MobaXTerm. 

- SSH command: `ssh <cwl>@sockeye.arc.ubc.ca`  
- If we want to specify a node for login, for example login01, the ssh command would be: `ssh <cwl>@login01.sockeye.arc.ubc.ca`

### Commands used while login for the first time

After accessing Sockeye, ...

- Where am I?: `pwd`
- What is my allocation?: `groups <cwl>`
- What is my storage and file limits?: `print_quota`
- Who is on my team?: `print_members`

### Directories of an allocation

> See: https://confluence.it.ubc.ca/x/8RotCw (“About Sockeye - Storage Volumes”)

- Home (read-only) : _/home/\<cwl\>_
  - Scripting
  - Configuration files

- Project (read-only): _/arc/project/\<alloc-code\>/_
  - Project data
  - Big/shared software or DB

- Scratch (read and write): _/scratch/\<alloc-code\>_
  - Batch jobs
  - Job scripts

### Transferring data to Sockeye

1. scp or rsync
  - File transfer video: https://wgschool.netlify.app/hpc/hpc-02-basics/
  - ARC Sockeye TUD Quick Start Guide: https://confluence.it.ubc.ca/x/Q4MmCg

2. sftp (TBC...)  
```
## login 
sftp <cwl>@dtn.sockeye.arc.ubc.ca

```

3. (Frequently used) Globus File Transfer (https://www.globus.org)



## Advance: Install and Set Up a Software

The software I am currently using is R(>=4.1.2). 
However, Sockeye only has a lower version of R, which may not meet our demand.
Therefore, we need to install another version of R, together with its dependencies (packages). 

I tried to install R directly to the server for several times but failed with a reason that I don't know how to deal with it. 

Thankfully, Sockeye also provides pre-built containers, called **singularity**, 
to install softwares that are not pre-installed on the server. 

Before we install the software, we can check whether there is such an image for the software or not. 
- Find pre-built container images:
  - Docker Hub: https://hub.docker.com/ **(Check this one)**
  - Singularity Hub: https://singularityhub.github.io/singularityhub-docs/ (Read-only and not maintained)

Then, use command:  
`singularity pull docker://rocker/rstudio` or `singularity pull --name rstudio.sif docker://rocker/rstudio` to specify a name.

> When checking the docker hub, we may see that the recommended installation command line for docker users are: `docker pull rocker/rstudio`. 
> Then, what we need to do is to change the command accordingly as shown above. 

Then, if successfully installed, a `.sif` file will exist in the path (folder) where we are now.  

> A funny thing is, the docker hub provides an image for rstudio, and also an image of r combined with tidyverse, but no image that combines r with rstudio and tidyverse. What's more surprising is that, the tidyverse pkg cannot be successfully installed to `rstudio.sif`... 
> So, I am currently using `tidyverse.sif` stored in `~/project/rstudio/`, and its customized name is `r-tidyverse.sif`. 

To use the installed `.sif` image, we first need to load module: `module load singularity` (and maybe together with another command `module load gcc`).
Then use the command: `singularity shell (/path/to/file/)r-tidyverse.sif`, and we would load into the image. 
After loading into container, enter `R` and then we can use R interactively on the server. 

```
$ module load gcc
$ module load singularity
$ singularity shell (/path/to/file/)r-tidyverse.sif 
> R 
> (R codes ...)


> quit()                # to exit R
> exit                  # to exit the singularity container
```


### Install and use the dependencies packages of R

When installing R packages, I'd like to run R interactively to check whether the package is successfully installed or not. 
So, follow the instruction written above, load in the `r-tidyverse.sif` image and then enter R, and go to the interactive UI.

A funny setting for Sockeye is that, if we install the R package to its default path, the installed packages will be deleted every time we quit R.
Therefore, we need to install the packages to another library path instead of its default path and then specify the library path everytime (?) before we load the packages in the Rscripts. 

The library path I create is `~/project/rstudio/Rlibs/`, which contains the R packages adaptive to R-4.2.0 (the R version of `r-tidyverse.sif`). 
Therefore, everytime we want to install a new package, or want to library a package we installed, code:
`.libPaths(~/project/rstudio/Rlibs/)` in the Rscript to specify an alternative library path.

```{r}
## Install a Package in R 

.libPaths(~/project/rstudio/Rlibs/)
.libPaths()                           # check whether the alternative path is specified.
install.packages("rsvd")              # the package will be installed to the specified path.
```

Notice that the packages installed together with the `.sif`, such as `tidyverse` and `MASS`, are stored in the default path instead of the new created path. 
Therefore, I recommend that these packages be loaded first, and then specify the alternative library path and the independent packages be loaded. Here is an example of an **Rscript**:

```{r}
## Load Packages

library(tidyverse)                     # load the attached packages
library(MASS)

.libPaths('~/project/rstudio/Rlibs/')  # specify the alternative path
library(rsvd)                          # load the independent packages
library(knockoff)

setwd("~/working/directory/")
```

## Advance II: Run R(.sif) on the Server

For most of the time, we don't need to run R interactively. 
What we want is to submit a job to the node and let it run the Rscripts. 

So, this is a most ordinary template for a `mytest.pbs` file, which should be stored in the `~/scratch/(\<cwl\>/)`:
```
#!/bin/bash
 
#PBS -l walltime=10:00:00,select=1:ncpus=1:mem=2gb
#PBS -N job_name
#PBS -A <alloc-code>
#PBS -m abe
#PBS -M <cwl>@student.ubc.ca
#PBS -o (job_name)_output.txt
#PBS -e (job_name)_error.txt
 
################################################################################
 
module load gcc
module load singularity
 
cd $PBS_O_WORKDIR
 
(date)
(hostname)
singularity exec /path/to/image.sif Rscript /path/to/file.R
(date)

```
 **Key points of the executing commands:**
 - The command of executing the `.sif` is `singularity exec *r.sif Rscript filename.R`.
 - To make sure the server find the `.sif` container, it'd better to enter the absolute path of the `.sif` container. 
 - The keyword **Rscript** is indispensible, and the codes should be written in an `.R` file instead of a `.Rmd` file.
 - The path of the Rscript should also be specified. Likewise, it's better to enter the absolute path of the `.R` file.
 - (date) and (hostname) are optional commands.

 > Details of batch job scripts are given in https://confluence.it.ubc.ca/display/UARC/Running+Jobs. 


To submit this job, (this is how I usually do) first `cd` to the path where this `.pbs` script is stored, 
and then `qsub` the `.pbs` script. job
And then use command `qstat -xu $USER` or `qstat -f <jobid>` to check the status of the submitted jobs.
Use command `qdel <jobid>` to delete the job.
```
cd /path/to/.pbs/file/
qsub name.pbs                 # to submit the job
qstat -f <jobid>.phsha        # to check the status of the specific job
qstat -xu $USER               # to check the status of jobs, including history job submissions
qstat -u $USER                # to check the status of current running jobs
qdel <jobid>.pbsha            # to delete the job
```

**(jobname)\_output.txt** and **(jobname)\_error.txt** will be created and stored under the path where the 
`.pbs` file is stored (because we have `cd`ed to that path before we submit the job).
 
 
### Advance 3: Run a Series of Jobs (Array Jobs) on the Server

To evaluate the effectiveness of a method in the statistical research, 
hundreds of thousands of simulations, with only several parameters changed, 
are usually needed to support the announcement that "our method is superb and groundbreaking!". 
In this case, a single job script can launch many jobs and change the execution parameters, 
such as input files, based on the array index within the job.
After submitting such a job script to the server, we can just wait for those tons of results come flooding back to us!  

Suppose the execution parameters have stored into `params.txt`. 
Here is an example of a pbs script named `mytest-array.pbs`:
```
#!/bin/bash
 
#PBS -l walltime=2:00:00,select=1:ncpus=1:mem=32gb
#PBS -J 1-18:3
#PBS -N mytest
#PBS -A <alloc-code>
#PBS -m abe
#PBS -M <cwl>@student.ubc.ca
#PBS -o mytest_output_^array_index^.txt
#PBS -e mytest_error_^array_index^.txt
 
################################################################################
 
module load gcc
module load singularity
 
echo I am job index: $PBS_ARRAY_INDEX

cd $PBS_O_WORKDIR

### Get Array ID
j=${PBS_ARRAY_INDEX}

### Parameter file to read
parameter_file="params.txt"
echo "Parameter file: ${parameter_file}"
echo

### Read line #j from the parameter file
PARAMETERS=$(sed "${j}q;d" ${parameter_file})
echo "Parameters are : ${PARAMETERS}"
echo

singularity exec /path/to/image.sif Rscript /path/to/file.R ${PARAMETERS}

```

__Key Commands/ Arguments:__(https://confluence.it.ubc.ca/display/UARC/Running+Jobs) 
- `#PBS -J 1-18:3`: This flag identifies the job as an array job and sets the indexes to be used for the sub jobs. 1-18 in this case gives a range of 1 to 18 for subjob indexes. This range can be any set of positive values to identify the indexes. Following the range there is an optional parameter that can be provided to identify the increment step between each job index. In this case we use 3 so the job indexes will be : 1, 4, 7, ..., 16. If no increment parameter is provided the increment defaults to 1.   
- `$PBS_ARRAY_INDEX`: During job execution this environment variable will contain the job array index number specific to the subjob. This can be used to map the job execution commands and parameters to the unique components for each sub job.
- `_^array_index^`: During job submission time the PBS_ARRAY_INDEX environmental variable is not yet initialized. If you need to define the job array index during submission time the macro ^array_index^ can be used and will be replaced on job submission by the array ID. This is particularly useful if you wish to use PBS to control the standard output and error with the -o and -e flags and would like each subjob in the array to write to its own file.
- `${PARAMETERS}`: As defined by the commands, this argument refers to the *j*-th line from the parameter file (where *j* is substitute for the job array index number announced by command `j=${PBS_ARRAY_INDEX}`). By adding this command to the end of `singularity exec /path/to/image.sif Rscript /path/to/file.R`, the specific parameters (as class *character*) are passed down to the Rscript. 

Accordingly, the Rscript uses the following command to receive the parameters:
```{r}
args <- commandArgs(trailingOnly = TRUE)  # as character
str(args)
cat(args, sep = "\n")                      # print parameters

arg1 <- args[1]
arg2 <- as.numeric(args[2])                 # change to numeric number (if applicable)
```

# Pitfalls I fell into when trying to run R on the server

> Ooooops forgot to save the edited document...
> Well, never mind.

Here are practical steps that help run an Rscript on Sockeye server successfully. 

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

2. (Frequently used) Globus File Transfer (https://www.globus.org)



## Advance: Install and Use a Software

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


 

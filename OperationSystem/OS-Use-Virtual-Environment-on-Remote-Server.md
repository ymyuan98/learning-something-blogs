# How to Create and Use a Virtual Environment on a Remote Server

Recently I need to install and run the package [`scarlink`](https://github.com/snehamitra/SCARlink), which is developed for multi-omics data (scATAC-seq and scRNA-seq) analysis. 
Since the multiomics data are usually large in size, it is not a good idea to run it on my local laptop. 
Therefore, I would like to install the package on a remote server. 
To do this, I need to create a virtual environment for the use of `scarlink`. 
However, a virtual environment cannot be created by 
```
conda create -n scarlink_env
```
because conda commaned does not exit under my account. 

Here are the steps I use, as suggested by claude-code:

## Step 1: If `conda` doesn't exist, install Miniconda (no sudo needed)
In bash (terminal), run
```{bash}
# Download the Miniconda installer script
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

# Install it
bash Miniconda3-latest-Linux-x86_64.sh
```
This allows us to use conda command to create virtual environments.
Press **yes** when asked a question like "Do you wish to initialize Miniconda3?".
By default, Miniconda will be installed under your (user) home directory. 

Then, close the current terminal and open a new terminal to make the conda command available.
Or, we can run
```{bash}
source ~/.bashrc
```
instead to make the conda command available in the current terminal. 

From now on, we can use the `conda` command to create virtual environments without sudo authorization!

## Step 2: Create a virtual environment
The next step is to create a virtual environment that is compatible with the package `scarlink`. 
Here, we would like to create a virtual environment named `scarlink_env` with *python=3.9*. 
So, what we need to do is to run the following command in bash (terminal):
```{bash}
conda create -n scarlink_env python=3.9
```
This creates a virtual environment named `scarlink_env` with Python version 3.9. 

If we want to want to create a virtual environment with other version of Python, just need to change the version number. 

Or, if we want to install other programming languages, such R, we can run:
```{bash}
conda create -n scarlink_env python=3.9 r-base
```
This creates a virtual environment named `scarlink_env` with Python version 3.9 and
R language installed.

## Step 3: Activate the virtual environment
To use the virtual environment, we need to activate it first.
In bash (terminal), run:
```{bash}
conda activate scarlink_env
```
Now, `(scarlink_env)` would be appear before `username@host`.
This indicates that the virtual environment `scarlink_env` is activated.

From now on, any packages installed in `scarlink_env` would not affect other virtual environments or the base environment. Great!

## Step 4: Install the package `scarlink` and its dependencies
Now, we can install the package `scarlink` and its dependencies in the virtual environment `scarlink_env`
by following the instructions in the Github page...

Notice! The required packages of `scarlink` requires Python version >= 3.9, which is inconsistent of the instruction page of `scarlink` that suggests to install Python version 3.8. 

> The `scarlink` package, I guess, is mainly designed for Linux system, as installing it on the Linux system is much much easier than on MacOS system. It makes sense, because usually the multi-omics data are large in size, and Linux server is more suitable for handling large data.

After installing all the packages, we can run `scarlink` package in this virtual environment! 

## Step 5: Deactivate the virtual environment
When finishing the tasks in the virtual environment, we can deactivate it by running:
```{bash}
conda deactivate
```
This returns us to the base environment. 
The prefix `(scarlink_env)` before `username@host` would disappear, indicating that we have exited the virtual environment `scarlink_env` and has returned back to `(base)`.

## Step 6: Remove the virtual environment (if needed)
If we no longer need the virtual environment, we can remove it by running:
```{bash}
conda remove -n scarlink_env --all
```
This removes the virtual environment `scarlink_env` and all its packages.
Then, to clean the cache, we can run:
```{bash}
conda clean --all -y
```

## Step 6: Additional notes
To check which virtual environments are available, we can run:
```{bash}
conda env list
```
This lists all the virtual environments created by conda.


## Step 7: More additional notes 
Thanks to internet and vibe-coding tools, now we don't need to memorize so much commands. Just check! 
But one thing I am clear now is that, we don't need *sudo* authorization to create virtual environments, or even using conda commands. 
What we need to do is just to install Miniconda first under our user home directory.


Finished!




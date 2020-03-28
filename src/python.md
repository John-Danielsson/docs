# Hyak python programming

## Anaconda Python

Anaconda is a Python distribution which contains most of the commonly used Python packages in scientific computing.

Use the below command to get an interactive build session on ikt.hyak and mox.hyak:

```srun -p build --time=3:00:00 --mem=10G --pty /bin/bash``` 

To find the latest anaconda module:

```module avail anaconda```

Use the below command to load the ananconda module:

```module load anaconda2_4.3.1```

_(For frequent Python users it is preferable to install your own copy of Anaconda by using below instructions in the section "Install Anaconda Python".)_

Ananconda comes with over 200 packages. Most packages which are useful for scientific computing such as matplotlib, numpy and scipy are already installed. If the package that you need is not already installed then you can install it using the pip command. Before installing python packages, check that "module list" does not show any icc modules (e.g. icc_17). If there are any icc modules then unload them (e.g. "module unload icc_17"). Also check that there is no command like "module load icc_17" in your .bashrc or .bash_profile.

For example, to install package "packagename" use the below pip command.
```pip install packagename --user```

**Notes**

(a) The option ```--user``` ensures that the package is installed to a location to which you have write permission.

(b) Replace "packagename" with the name of the package that you want to install.

(c) If you are using Python 3.x, then use pip3 instead of pip in above commands.

If the above ```pip install``` fails then use these steps to clean up:

```pip uninstall packagename```

```cd ~/.cache```

```rm -rf pip```

Some packages may require some additional steps. For example, see these links for how to install mpi4py or cutadapt.

[Hyak/Ikt mpi4py](hyak_mpi4py.md)

[Mox mpi4py](mox_mpi4py.md)

[Hyak cutadapt](cutadapt.md)

Note that ```conda install``` is an alternative to ```pip install``` and conda is preferred by some users. However, **do not use** ```pip install``` and ```conda install``` interchangeably. Either use only ```pip install``` or use only ```conda install```.

## conda environment (optional)

You can create your own conda virtual environment called ```myenv``` with the following steps:

```module load anaconda2_4.3.1```

```conda create --name myenv pip```    

```source activate myenv```

The prompt will change like below. Now you can install packages via pip without using the ```--user``` option.

```(myenv)$ pip install simplejson```

Use this command to exit the myenv virtual envrionment:

```(myenv)$source deactivate```

To delete the myenv virtual environment.

```$ conda remove --name myenv --all```

Instead of the above ```pip install simplejson```, you can also use ```conda install simplejson```. However as mentioned earlier, **do not use** ```pip install``` and ```conda install``` interchangeably. Either use only ```pip install``` or use only ```conda install```. For more details see [this link](https://www.anaconda.com/using-pip-in-a-conda-environment/).

**Installing Anaconda Python (recommended)**

As usual, "groupname" is your group name and "userid" is your user ID.

First, get an interactive build node as noted above.

On your desktop or laptop go to https://www.anaconda.com/download/ and click on the Linux penguin. Decide if you want Python 2.7 or Python 3.x. Right click on the 2.7 or 3.x  green button and copy link location (for example https://repo.continuum.io/archive/Anaconda2-5.0.0-Linux-x86_64.sh).

On a Hyak interactive build node, issue a command like this one:

```wget https://repo.continuum.io/archive/Anaconda2-5.0.0-Linux-x86_64.sh``` 

```bash Anaconda2-5.0.0-Linux-x86_64.sh```

Install to a location like /gscratch/groupname/userid/anaconda3 or /gscratch/groupname/userid/anaconda2.
_(The default location is anaconda2 or anaconda3 in your home directory. Since there is a disk quota on your home directory, it is better to install to /gscratch/groupname/userid. However, if your only group is stf then you should install in your home directory since /gscratch/stf is scrubbed periodically and older files are deleted.)_

Put this in your .bashrc:

```export PATH=/gscratch/groupname/userid/anaconda2/bin:$PATH```

Now, issue this command:

```bash```

Then, issue this command to verify that you are using the version of Python which you just installed:

```which python```

Finally, issue this command to find your anaconda version:

```conda list anaconda```

**Intel Python (optional)**

Intel Python is also available on Hyak. It is based on Anaconda Python and is optimized for Intel hardware. It already has mpi4py.

Use this command to find the latest Intel Python module:

```module avail intel```

To load Intel Python:

```module load intel-python2_2017```

**Notes**

(1) Intel Python has most but not all of the modules of Anaconda Python or standard Python. Use below command to find the list of modules:

```python```

```>>>help('modules')```

(2) Intel Python does not have the Standard Python readline module since the readline has a GPL license. Hence, the arrow keys do not work at the Intel Python interactive prompt. You can use Anaconda Python at the interactive prompt and Intel Python to run Python scripts. 

**Python multiprocessing module**

If your program is using the Python multiprocessing module then use the options ```--nodes=1 –-ntasks-per-node=28``` with your ```srun``` command or sbatch script to ensure that your program can use 28 cores on the node. Note that the Python multiprocessing module only allows single node parallel programs.

**Making Python 2.7 behave like Python 3.0**

You can use below ```from __future__``` statements to make Python 2.7 behave like Python 3.0
 
```from __future__ import print_function```

```from __future__ import division``` 

```from __future__ import absolute_import```

```from __future__ import unicode_literals```


More details can be found [here](https://docs.python.org/3/library/__future__.html).
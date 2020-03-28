# Hyak mpi4py

Below is for ikt.hyak. For mox.hyak see [this link](mox_mpi4py.md). 
 
The Python package mpi4py provides bindings for the Message Passing Interface (MPI) standard.
Here is [a link](http://mpi4py.readthedocs.org/en/stable/tutorial.html) to the documentation for mpi4py.

Install your own copy of Python. See section on "Installing Anaconda Python" in this link:
[Hyak python programming](python.md)

Issue the below command to verify that you are using your own copy of Anaconda Python. The path which is printed should contain your own userid.
```which python```
 
**Anaconda Python**
Below are the steps to install mpi4py on ikt.hyak.
```srun -p build --time=2:00:00 --mem=100G --pty /bin/bash```
```module load gcc_4.4.7-ompi_1.6.5```
```pip install --user mpi4py```
You can verify that the install was fine by doing below steps in the interactive node:
```export MX_RCACHE=0```
```mpirun -np 16 python my_mpi_program.py```
After the install, you can use the mpi4py package in your Python programs.

**Interactive Node**
For use on an interactive node. One ikt node can run 16 processes. During an interactive session, you cannot run across multiple nodes. 
```module load gcc_4.4.7-ompi_1.6.5```
```export MX_RCACHE=0```
```mpirun -np 16 python my_mpi_program.py```

**Batch job**
For a batch job, your slurm script, should contain lines like those below. Do not specify the ```-np``` option because mpirun will automatically run across all the cores on all the nodes allocated to your batch job by Slurm.
```#below is for batch job on ikt```
```module load gcc_4.4.7-ompi_1.6.5```
```export MX_RCACHE=0```
```mpirun --mca mtl mx --mca pml cm --bind-to-core python my_mpi_program.py```

**Intel Python**
Intel Python is also available on Hyak. It is based on Anaconda Python and is optimized for Intel hardware. Use the below command to load Intel Python. It already includes mpi4py.
```module load intel-python_2017```
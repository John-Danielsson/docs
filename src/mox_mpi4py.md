# Mox mpi4py

This section is for mox.hyak. For ikt.hyak, see [this link](hyak_mpi4py.md).

The Python package mpi4py provides bindings for the Message Passing Interface (MPI) standard.
Here is [a link](http://mpi4py.readthedocs.org/en/stable/tutorial.html) to the documentation for mpi4py:

Install your own copy of Python. See section on "Installing Anaconda Python" in this link:

[Hyak python programming](python.md)

Issue the below command to verify that you are using your own copy of Anaconda Python. The path which is printed should contain your own userid.

```which python```

**Installing mpi4py**

```module load gcc_4.8.5-impi_2017```

```pip install mpi4py --user```

**Running a mpi4py program**

One mox node can run 28 processes. During an interactive session, you cannot run across multiple nodes. 

```module load gcc_4.8.5-impi_2017```

```mpirun -n 28 python my_mpi_program.py```

In a batch job your mpi program can run across multiple nodes. In a batch job, do not specify the ```-n``` option since mpirun will automatically run across all the cores on all the nodes allocated to your batch job.

See the link below for a sample MPI sbatch script.

[Mox MPI](mox_mpi.md)
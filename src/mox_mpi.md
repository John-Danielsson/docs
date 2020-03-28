# Mox MPI

As usual, "groupname" is your group name and "userid" is your user ID.
## Compiling an MPI program
See also [Hyak Intel MPI](hyak_intel_mpi.md).

Load one of the below modules.
### Intel
#### Intel Mox
```module load icc_18-impi_2018```
#### Intel Ikt
```module load icc_18-impi_2018```
### gcc
#### gcc Mox
```module load gcc_4.8.5-impi_2017``` 
#### gcc Ikt
```module load gcc_4.4.7-impi_5.1.2```

Compile your program using one of the below compilers:
- Intel C compiler is mpiicc
- Intel C++ compiler is mpiicpc
- Intel Fortran compiler is mpiifort
- GNU C compiler is mpigcc
- GNU C++ compiler is mpigxx
- GNU Fortran compiler mpifc
- Generic C compiler is mpicc
- Generic C++ compiler is mpiCC
- Generic Fortran compiler mpif90

Example:
```mpicc my_mpi_program.c -o my_mpi_program```

Above, generic means it could be Intel or GNU compiler depending on what module was loaded. This command shows details of mpicc and the compiler used:
```mpicc -show```

Use this command to find location of MPI:
```which mpirun```

## Running an MPI program

More details are at https://slurm.schedmd.com/mpi_guide.html.

## Interactive MPI
The following must be same module which was used to compile the program:
```module load icc_17-impi_2017```
 
For Ikt, use the below line. _Do not use it for Mox._
```export MX_RCACHE=0```
 
While running in an interactive node you must give the ```-np``` option. The ```-np``` option is not required for batch jobs. Use full path or cd to the appropriate directory and use ```mpirun -np 28 ./my_mpi_program```.

Example:
```mpirun -np 28 /gscratch/groupname/userid/my_mpi_dir/my_mpi_program```

## Batch MPI
Below is a sample MPI Job Script myscript.slurm. Change the options as needed and submit from the login node using ```sbatch myscript.slurm```.

#!/bin/bash

##Job Name

#SBATCH ```--job-name=my_mpi_job```

##Allocation Definition 

#SBATCH ```--account=groupname``` 

#SBATCH ```--partition=groupname```

##Resources

##Total number of nodes

#SBATCH ```--nodes=2```  

##Number of cores per node

#SBATCH ```--ntasks-per-node=2```8

##Walltime (2 hours)

#SBATCH ```--time=2:00:00```

##Memory per node

#SBATCH ```--mem=100G```

##Specify the working directory for this job.

##Make this directory before submiting this job.

#SBATCH ```--chdir=/gscratch/groupname/userid/my_mpi_dir```

##turn on e-mail notification

#SBATCH ```--mail-type=ALL```

#SBATCH ```--mail-user=your_email_address```

##For Ikt, use the below line. Delete it for Mox.

```export MX_RCACHE=0```

##Below must be same module which was used to compile the program.

```module load icc_17-impi_2017```

##Use full path or cd to the appropriate directory and use ```mpirun ./my_mpi_program```.

##Here in batch mode we do not need to use ```-np``` option with ```mpirun```.

```mpirun /gscratch/groupname/userid/my_mpi_dir/my_mpi_program```
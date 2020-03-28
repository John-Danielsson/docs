# PETSc

Detailed PETSc installation instructions are at [this link](http://www.mcs.anl.gov/petsc/documentation/installation.html).

Below are some sample instructions for installing PETSc on Hyak, where "groupname" is your group name and "userid" is your user ID. You may also want to download and build the latest version rather than the version we use in this example.

Use the below commands on the login node to get a build node:

On ikt.hyak and mox.hyak:
```srun -p build --time=3:00:00 --mem=10G --pty /bin/bash```
 
```mkdir /gscratch/groupname/userid/petscstuff```

```mkdir /gscratch/groupname/userid/petscinstall```

```cd /gscratch/groupname/userid/petscstuff```

```wget http://ftp.mcs.anl.gov/pub/petsc/release-snapshots/petsc-3.5.3.tar.gz```

```tar -xvf petsc-3.5.3.tar.gz```

```cd /gscratch/groupname/userid/petscstuff/petsc-3.5.3```

## GNU Compiler Build Instructions

Use the below command to find the latest gcc and impi version on ikt.hyak or mox.hyak:
```module avail gcc```

In the below instructions, use the latest GCC and IMPI version from the output of the above module command. Also modify ```--with-mpi-dir=/sw/intel/impi/5.0.2.044/intel64``` as appropriate.

On ikt.hyak, use the below instructions:
```module load gcc_4.4.7-impi_5.0.2```
 
```./configure   --download-fblaslapack --prefix=/gscratch/ABC/xyz/petscinstall \```

```--with-mpi-dir=/sw/intel/impi/5.0.2.044/intel64```
 
```make PETSC_DIR=/gscratch/ABC/xyz/petscstuff/petsc-3.5.3 PETSC_ARCH=arch-linux2-c-debug all```
 
```make PETSC_DIR=/gscratch/ABC/xyz/petscstuff/petsc-3.5.3 PETSC_ARCH=arch-linux2-c-debug install```
 
On mox.hyak, use below instructions:

```module gcc_4.8.5-impi_2017```
 
```./configure --download-fblaslapack --prefix=/gscratch/ABC/xyz/petscinstall \``` 

```--with-mpi-dir=/gscratch/sw/intel-201703/compilers_and_libraries_2017.2.174/linux/mpi/intel64```
 
```make PETSC_DIR=/gscratch/ABC/xyz/petscstuff/petsc-3.5.3 PETSC_ARCH=arch-linux2-c-debug all```
 
```make PETSC_DIR=/gscratch/ABC/xyz/petscstuff/petsc-3.5.3 PETSC_ARCH=arch-linux2-c-debug install```
 
Please refer to Hyak's sample job scripts for ikt.hyak and mox.hyak:

[Mox MPI](mox_mpi.md)
 
When you run PETSc in your slurm script, you should include the same module you used when you built PETSc.
 
**Intel Compiler Build Instructions**

The Intel suite of software development products generally produce the fastest code on Hyak. Use the following command to find the latest icc and impi version on ikt.hyak or mox.hyak:

```module avail icc```

In the instructions below, use the latest icc and impi version from output of above module command.

```module load icc_<version>-impi_<version>```

```./configure --prefix=/gscratch/ABC/xyz/petsc-intel --with-mpi-dir=$I_MPI_ROOT --with-blas-lapack-dir=$MKLROOT \```

```--with-scalapack-lib="-L$\{MKLROOT\}/lib/intel64 -lmkl_scalapack_lp64 -lmkl_blacs_intelmpi_lp64" --with-cc=$CC \```

```--with-cxx=$CXX --with-fc=$FC PETSC_ARCH=linux-gnu-intel```

```make PETSC_ARCH=linux-gnu-intel PETSC_DIR=$PWD all```
 
Please refer to Hyak's sample job scripts for ikt.hyak and mox.hyak:

[Mox MPI](mox_mpi.md)
 
When you run PETSc in your slurm scripts, you should include the same module you used when you built PETSc.
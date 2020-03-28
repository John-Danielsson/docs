# Hyak Intel MPI

Use ```module avail``` to decide the Intel compiler icc version and Intel MPI impi version that you want to use. Then load it using this command:

```module load icc_14.0.3-impi_4.1.3.049```

Now you can use the intel compiler for MPI, mpiifort, mpiicc etc. However, it may be that your Makefiles contains the non-Intel specfic compiler names like mpif90, mpicc, mpicxx etc. As a result, you can export the below environment variables for the bash shell (Hyak's default shell is the bash shell):

```export I_MPI_CC=icc```

```export I_MPI_CXX=icpc```

```export I_MPI_F77=ifort```

```export I_MPI_F90=ifort```

Some users may be using C-shell due to historical reasons. The equivalent commands for the C-shell are:

```setenv I_MPI_CC icc```

```setenv I_MPI_CXX icpc```

```setenv I_MPI_F77 ifort```

```setenv I_MPI_F90 ifort```

Now mpif90, mpicc etc. will use Intel MPI. So if your Makefile contains mpif90, mpicc etc. then it will use Intel MPI. Another alternative is to modify your Makefile with an option. For example, for mpif90 you can use the below option to tell mpif90 to use the intel compiler ifort:

```mpif90 -f90=ifort ...```

Use the below command to find the full list of options used by mpicc:

```mpicc -show```
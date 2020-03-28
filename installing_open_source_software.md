# Installing Open-Source Software

First get an interactive build node as described in this link:
[Building Code](building_code.md)

Use the below command to see if the software that you want is already installed on Hyak:

```module avail```

If the software "softwarename" is already available then use the below command to set up your environment for using the software:

```module load softwarename```

If the software is not available then you will need to install it. You may be used to installing software on your desktop machine as root or administrator. However, it is not necessary to be root or administrator to install software on Hyak. Almost all closed source software allows you to choose the installation path during the install process.

Below are the steps for installing open source software. As usual, "groupname" is your group name on hyak and "username" is your user ID on hyak. The below steps are for installing glpk. Modify the steps as appropriate for your software.

```srun -p build --time=2:00:00 --mem=10G --pty /bin/bash```

```mkdir glpkstuff```

```cd glpkstuff```

```wget http://ftp.gnu.org/gnu/glpk/glpk-4.54.tar.gz```

```gunzip glpk-4.54.tar.gz```

```tar -xvf glpk-4.54.tar```

```cd glpk-4.54```

```./configure --prefix=/gscratch/groupname/username/myinstalldir/glpk-4.54install```

```make```

```make install```

Now the software is installed in the directory given by the --prefix option. Look inside the directory for the bin directory and put the complete path to the bin directory in your PATH variable in your .bashrc like so:

```export PATH=/gscratch/groupname/username/myinstalldir/glpk-4.54install/bin:$PATH```

Log out and log in again, then issue the command for your newly installed software.

**Notes**
1) The above description is for the basic steps. If you want more options for installing and compiling the software then use the below command to find the possible choices:
```./configure -help``` (or -h or --help) gives details about options)

2) ```--pty /bin/bash``` must be the last option in above command.

3) If you do not get a build node with the above values of --mem, then try smaller values.
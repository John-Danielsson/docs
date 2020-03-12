# Hyak MATLAB programming

As usual, "username" is your userid and "groupname" is your group name.

## Interactive Use of MATLAB

```ssh -X username@mox.hyak.uw.edu```

```srun -p xyz -A groupname --nodes=1 --ntasks-per-node=28  --time=2:00:00 --mem=100G --pty /bin/bash```

The below command shows you the available matlab versions on hyak:

```module avail matlab```

Use this command to load matlab and start the GUI:

```export TZ="America/Los_Angeles"```

```module load matlab_2018a```

```matlab```

After the above step, you can continue with your matlab commands at the matlab command line.

For batch use of matlab, include the below lines in your slurm script. Do not use -nojvm option of matlab. The -nojvm option prevents usage of parpool for matlab parallel computing.) Here the file name of the matlab script is myfunction.m.

```export TZ="America/Los_Angeles"```

```module load matlab_2018a```

```matlab -nosplash -nodisplay -r myfunction```

## Environment Variables in MATLAB

Inside your matlab script, you can access environment variables. For example, to get the value of the environment variable ```SLURM_ARRAY_TASK_ID``` use these lines:

```taskIDstring = getenv('SLURM_ARRAY_TASK_ID');```

```taskIDnumber = str2num(taskIDstring);```

## Matlab Toolboxes on Hyak:

Obtain an interactive node as shown above. Then type the below commands to get a MATLAB prompt.

```export TZ="America/Los_Angeles"```

```module load matlab_2018a```

```matlab -nosplash -nodisplay```

At the matlab prompt enter this command to find the list of toolboxes:

```>>ver```

We have a large number of toolboxes. Here are some popular toolboxes:
- Image Processing Toolbox
- Optimization Toolbox
- Parallel Computing Toolbox
- Signal Processing Toolbox
- Statistics and Machine Learning Toolbox

## MATLAB Parallel Programming on Hyak:

Do not use the build node for matlab parallel programming. The build node is for single core use. Hence, for the build node, the MATLAB command "feature('numcores')" will return 1.

## For Interactive Use:

At the MATLAB prompt enter below command to start the parallel pool:

```>> numCores = feature('numcores');```

```>> parpool(numCores)```

Now you can use parfor, etc. (If you run parpool without giving the number of cores, then the default number of cores is 12. It is _not_ the number of cores on the machine; "numCores" above is the number of cores allocated to your job by Slurm.)

## For Scripting

Add the below line to your slurm script before the matlab command. This creates a temporary directory which Matlab will use to store its cluster information.

```mkdir -p /gscratch/groupname/username/$SLURM_JOB_ID```
 
The file trypar.m contains the below code. A semicolon after a matlab command means that matlab will not print the output of that command. (For the most common type of nodes on mox, numcores=28. For mox nodes from 2019 or later, numcores=32. For latest mox nodes numcores=40. For ikt nodes, numcores=16.)

1) Create a local cluster object.
myCluster = ```parcluster('local');```
2) Set the JobStorageLocation to the temporary directory that was created in your slurm script.
```myCluster.JobStorageLocation = strcat('/gscratch/groupname/username/', getenv('SLURM_JOB_ID'));```

```numCores = feature('numcores');```

```parpool(numCores);```

```parfor i=1:1000000 ```

```A(i)=i*i;```

```end```

```A```

```quit```
 
You can run trypar.m in parallel at the prompt of an hyak interactive node:

```export TZ="America/Los_Angeles"```

```module load matlab_2018a```

```matlab -nodisplay -nosplash -r trypar```

You can run trypar.m in parallel using below lines in your slurm script. Here groupname is the name of your group, username is your userid, mydir is the directory which contains trypar.m and trypar.pbs.
 
```export TZ="America/Los_Angeles"```

```module load matlab_2018a```

```matlab -nodisplay -nosplash -r trypar```

## MATLAB STDOUT, STDERR, STDIN

In fprintf(), fileID=1 is for printing to STDOUT and fileID=2 is for printing to STDERR.

[More information on fprintf().](https://www.mathworks.com/help/matlab/ref/fprintf.html)

[More information on disp().](https://www.mathworks.com/help/matlab/ref/disp.html)

Use this to get data from STDIN:

```x = input('', 's')```

[More Information On input()](https://www.mathworks.com/help/matlab/ref/input.html)

[Matlab Code Performance](https://www.mathworks.com/help/matlab/code-performance.html)

[Matlab Memory Usage](https://www.mathworks.com/help/matlab/memory.html)

[Matlab Java Memory Usage](https://www.mathworks.com/help/matlab/matlab_external/java-heap-memory-preferences.html)


_======== Ignore below text. It is here for historical reasons. ===========_

_myCluster.NumWorkers = 28;_  

_saveProfile(myCluster);_
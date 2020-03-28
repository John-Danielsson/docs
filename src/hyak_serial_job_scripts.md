# Hyak Serial Job Scripts

## Node Level Scheduling

Hyak uses node level scheduling. This means that single threaded jobs need to be bundled together since each job must use all the CPUs on the node to which your job is assigned. Below you will find two scripts that should give you some ideas about how you can do this. You will need to change parameters appropriately for your job.

## GNU Parallel
The examples below both use GNU Parallel which is installed on Hyak. Given a list of programs, scripts, or arguments, GNU Parallel keeps a given number of processes running until the list has been exhausted. Please take a look at the GNU Parallel web page for a lot more examples about how you can use it. **GNU parallel does not start up processes on remote nodes.** If you want to use multiple nodes for your serial tasks, you must submit multiple jobs.

If you use parallel in your research please cite the author of the tool as follows, "O. Tange (2011): GNU Parallel - The Command-Line Power Tool, ;login: The USENIX Magazine, February 2011:42-47."

The GNU parallel command shown in below examples should be at the bottom of the slurm script shown at the end of [this link](mox_scheduler.md).

In the examples below, for Ikt, use ```-j 16```. For Mox, use ```-j 28```.

## Example 1

File mytasklist contains lines like so:

```command1```

```command2```

```command3```

and so on.

Each line is independent of every other line.

The command to run GNU parallel is shown below:

```cat mytasklist | parallel -j 16 --joblog  paralleljobs.log  --resume```

## Example 2

In this example, you set up a file containing a list of arguments you want to pass to multiple instances of your applications. This scripts will execute multiple instances of your application using the list of arguments you provide in myjobargs to keep all the CPUs busy on your assigned node until it runs out of arguments to pass. parallel will record when instances complete running in the file specified as an argument to ```--joblog```. Subsequent runs will only run instances that have not completed. You will need to change parameters  for your job. 

The file myjobargs contains lines like these:

```-i myinput1 -o myoutput1```
```-i myinput2 -o myoutput2```
```-i myinput3 -o myoutput3```

and so on.

The command to run GNU parallel is shown below:

```cat taskargs | parallel -j 16 --joblog  paralleljobs.log  --resume  /gscratch/xyz/abc/myapp```
 
## Example 3

In this example, you set up a directory containing an arbitrary number of executable scripts (taskscript1, taskscript2 etc.). This job will execute all the taskscript* scripts in that directory keeping all the CPUs on your assigned node busy until it runs out of scripts. These scripts should be general shell scripts to run your application, not slurm job submission scripts. GNU parallel will record when instances complete running in the file specified as an argument to ```--joblog```. Subsequent runs will only run instances that have not completed. You will need to change parameters appropriately. 

```find . -name "taskscript*" | parallel -j 16 --joblog paralleltasks.log --resume```
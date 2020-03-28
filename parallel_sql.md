# Hyak parallel-sql

Hyak's parallel-sql is very similar to standard GNU parallel, but instead of getting tasks from STDIN or a file, instances of parallel-sql retrieve unique tasks out of an SQL database as needed until all the tasks are complete. This allows users to build a single database of all their tasks and have many jobs (nodes) work from that single database.

## A More Detailed Description

parallel-sql takes tasks out of the database as it needs them. If you load ten thousand tasks into the database, then submit three 16 core jobs, those three jobs start and they each pick up 16 tasks. Once a task finishes, it's marked done and that instance of parallel-sql will retrieve another task from the database. The parallel-sql jobs will continue running like that until all the tasks are complete. If a job is preempted or canceled, all the picked up (running) tasks are marked as available so that other jobs or subsequent runs of the same job can pick them up and complete them.

**Definitions**

Task: a single work unit. For instance, a path to a program (e.g. Matlab) and a set of command line arguments (input parameters). A task must use only one core.

Job: an instance of parallel-sql executing or waiting to execute on a single node. Each job is defined by slurm job script and submitted to the scheduler separately. Each job runs on exactly one node at a time.

## Comparison to GNU parallel

With standard GNU parallel, you would have a project consisting of many thousands of tasks, but you'd have to split that up into multiple task lists (one for each job). You'd then submit N different jobs with N different task lists and job scripts. With parallel-sql you just load all the tasks you need to run into the database and submit a number of identical jobs equal to the number of nodes you'd like to use.

These links have information on GNU parallel:

For ikt.hyak:
https://wiki.cac.washington.edu/display/hyakusers/Hyak+gnu+parallel

For mox.hyak:
https://wiki.cac.washington.edu/display/hyakusers/Mox_gnu_parallel

## Important Notes

It is important to choose a number of jobs that's proportionate to the number of tasks you've added to your database. If you only have 600 tasks (each taking an hour), you should not submit more than thirty-seven 16 core jobs (592 total cores).

Standard requirements for the ckpt queue still apply. Also see the section below for specific instructions on the ckpt queue. Individual jobs should still run for at least 1 hour. This means thinking about the number of tasks you have to run and their runtime to determine the number of jobs you should submit. For example, if you have 10000 tasks that each run for 10 minutes, that's 100,000 minutes of computation (~28 hours). A 16 core node can perform 16 hours of computation per hour, so you should only use one or two 16 core nodes.

The ckpt queue is still unsuitable for tasks taking longer than 4 hours (unless your application performs checkpointing). parallel-sql does not checkpoint the progress of your application, only whether a task has completed or not.

If you have very long running tasks (that you run in your group's own allocation) and don't run a large number of tasks, standard GNU parallel is likely a better choice.

## Using parallel-sql

**Important Notes**

Do not perform more than one load operation at a time.

Do not perform a load operation while parallel-sql jobs are running.

**Initial Database Setup**

This creates a user and table for you on the database server where information about your jobs will be stored. You perform these steps only one time.

```login$ module load parallel_sql```

```login$ sudo pssu --initial```

```[sudo] password for USERID: <Enter your UW NetID password>```

**Basic Setup Session**

```login$ module load parallel_sql```

```login$ cat mytasklist | psu --load```

Do not perform more than one load operation at a time or load tasks while parallel-sql jobs are running.

```login$ psu --stats```

```100000  available```

```0       picked up```

```0       complete```

In above output, 'available' means the task has not started, and 'complete' means the task has completed. A 'picked up' task may actually be in three states: (1) it is running but not complete, (2) it was running but got interrupted by the scheduler (e.g. preempted or ran out of walltime), (3) an instance of parallel-sql has checked out the task from the server, but has not started running the task yet.

Below is an example of a task list. Each task is one line of the task list. Each task must be a complete command which can be run at the command line without any user input. Each task must be independent of every other task since  parallel-sql may run the tasks in any order.

## Example Task List "mytasklist"

```/gscratch/GROUP/USER/MYAPP -input /gscratch/GROUP/USER/b1/t1/input1 -output /gscratch/GROUP/USER/b1/t1/output1```
```/gscratch/GROUP/USER/MYAPP -input /gscratch/GROUP/USER/b1/t2/input2 -output /gscratch/GROUP/USER/b1/t2/output2```
...

```/gscratch/GROUP/USER/MYAPP -input /gscratch/GROUP/USER/b1/tN/inputN -output /gscratch/GROUP/USER/b1/tN/outputN```

## Sample Job Script

You will need to change parameters appropriately. Below "userid" is your user ID and "groupname" is your group.
 
#!/bin/bash
 
##Job Name
 
#SBATCH --job-name=myjob
 
##Allocation Definition
 
#SBATCH --account=groupname
 
#SBATCH --partition=groupname
 
##Resources
 
##Nodes. This should always be 1 for parallel-sql.
 
#SBATCH --nodes=1    
 
##Walltime (3 hours)
 
#SBATCH --time=3:00:00
 
##Memory per node
 
#SBATCH --mem=100G
 
##Specify the working directory for this job  
 
#SBATCH --chdir=/gscratch/groupname/userid/myjobdir
 
##turn on e-mail notification
 
#SBATCH --mail-type=ALL
 
#SBATCH --mail-user=your_email_address
 
```module load parallel_sql```
 
#Put commands here to load other modules (e.g. matlab etc.)
 
#Below command means that parallel_sql will get tasks from the database

#and run them on the node (in parallel). So a 16 core node will have

#16 tasks running at one time.

```parallel-sql --sql -a parallel --exit-on-term```

## Submitting Jobs

parallel-sql does single node parallelism. If you want to use multiple nodes, you will have to submit the same job script a number of times. If you use the bash shell (default), you can use the below example to submit a number of jobs specified by N.

```for job in $(seq 1 N); do sbatch parallel_sql_job.sh; done```

## Checkpoint Queue ckpt (old name backfill queue bf)

If your job gets interrupted and put back on the ckpt queue then when it starts again, parallel-sql will only run those tasks which were not completed earlier. The specific task which was interrupted will be in the 'picked up' state. So some of your tasks may end up in the 'picked up' state due to running out of walltime or when your jobs are preempted (interrupted). Hence, you can use below command to change the state of the tasks from 'picked up' to 'available'. This uses the scheduler data to the change the state for only those tasks which are not running.

```psu --reset-sched```

If your want to forcibly change the state of the tasks from 'picked up' to 'available' then you can use below command. This will change the state even if the task is running. Hence, you should only use it if you are sure that the task is not running.

```psu --reset```

## Database Maintenance

Once you're done with a set of tasks, you should clear them out of the database. --del removes all tasks. There's a maximum taskid of about 4 billion which you may hit if you never clean up your database with --del. With the default index type, hash, load performance decreases as the taskid increases.

Usage: psu [SINGLE ACTION] [--sql-set setname]

  ACTIONS

    --load             load tasks from STDIN

    --stats            show task counts

    --show-sets        list additional task sets

    --show-sizes       show sizes of all task sets

    --del              delete all tasks (add -y for no prompt)

    --reset            set picked up tasks to available (add -y for no prompt)

    --reset-sched      set picked up tasks to available with scheduler data

    --reset-all        set all tasks to available (add -y for no prompt)

    --show-nonzero     show completed tasks with non-zero exit code

    --reset-nonzero    set completed tasks with non-zero exit code to available

    --show-comp        show completed tasks

    --show-avail       show tasks that have not been picked up

    --show-running     show tasks that are running/picked up

    --show-index       show the index type

    --set-index        set the index type [btree|hash] (ADVANCED USE ONLY)

## Task Sets
parallel-sql can support multiple distinct sets of tasks. You might want to set up one set of tasks that can run in the ckpt queue since those tasks run for four hours or less and then set up another set of tasks that run in the batch queue because those tasks run for more than 4 hours.

```login$ module load parallel_sql```

```login$ sudo pssu --create-set ckpt```

```[sudo] password for USERID: <Enter your UW NetID password>```

```login$ cat mytasks | psu --load --sql-set ckpt```

```login$ psu --stats --sql-set ckpt```

```100000  available```

```0       picked up```

```0       complete```

```login$ sudo pssu --del-set ckpt```

```Are you sure you want to delete set 'ckpt' (Y/n)? Y```

You would then just have to add the ```--sql-set ckpt``` argument onto the command line for parallel-sql and that instance would read tasks from the ckpt task set.
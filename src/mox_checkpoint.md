# Mox Checkpoint

The Mox and Ikt checkpoint queue ckpt allows you to use nodes which are not being used by the node owner. The ckpt queue can only be used for batch jobs. 

A job on the ckpt queue can get interrupted at any time, so any program running on ckpt should either have a very short running time (e.g. a few minutes) or be able to checkpoint. 

A job is interrupted when the owner of the node submits a job or after it has run 4-5 hours. After a job is interrupted, it is put back in the ckpt queue so the program should be able to restart from the previous checkpoint. 

Note that the ```--time``` option must be greater than 5 hours to ensure that the job is put back in the ckpt queue after an earlier interruption.

If xyz is your group name then your slurm script should have lines like below:

#SBATCH --account=xyz-ckpt

#SBATCH --partition=ckpt

#SBATCH --nodes=1

#SBATCH --ntasks-per-node=28

#SBATCH --mem=100G

#SBATCH --time=12:00:00

Or you can provide the same information at the command line:

```sbatch -p ckpt -A xyz-ckpt  --mem=100G --time=12:00:00 myscript.slurm```

The ckpt queue is not suitable for multiple node jobs. A user will have to wait longer to get a block of 10 nodes than to get a single node. Even after the user gets those 10 nodes, if the owners of any one of the 10 nodes submit a job to their own queue then the user's job will get interrupted. Hence the probability of a 10-node job getting interrupted is much higher than  the probability a one node job getting interrupted.

Question: Why do my ckpt jobs not run right away even though "sinfo -p ckpt" shows many nodes are idle?

Answer: This is primarily due to a combination of two things:

(1) Due to the upcoming maintenance reservation, there are many jobs in the 'pending' (PD) state.

(2) If there are any jobs pending (regardless of the reason for the job still pending) in a partition with a higher Priority (e.g. stf), no jobs from a lower Priority partition (e.g. ckpt) will be launched on nodes that are shared in common. (This information is from SchedMD.)
The ckpt partition has a lower priority than all other partitions so that preemption can happen. Note that the ckpt partition does not have its own nodes so all ckpt nodes are shared with the node owning partition, e.g. stf.
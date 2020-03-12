# Building Code on Hyak

For building code on hyak you can get an interactive session using the below commands.

For mox.hyak:
    
```srun -p build --time=2:00:00 --mem=10G --pty /bin/bash```

For ikt.hyak:

```srun -p build --time=2:00:00 --mem=4G --pty /bin/bash```

You can then choose your compiler using the module command and continue with building your code.

**Notes**

1) ```--pty /bin/bash``` must be the last option in above command.

2) If you do not get a build node with above values of ```--mem```, then try smaller values.
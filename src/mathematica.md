# Mathematica on Hyak
## Support
Premier [support](https://www.wolfram.com/support/contact/) is available to users from Wolfram through the UW's site license. There is also extensive Mathematica [documentation](https://reference.wolfram.com/language/?source=nav) available on the support site.
## Licensing
Hyak is licensed to run jobs utilizing up to 8000 cores. These cores are shared among all Hyak users. 
## Mathematica GUI
You must have an X11 server on your workstation and X11 forwarding must be set up. As usual, "username" is your userid and "groupname" is your group name.
```ssh -X username@hyak.washington.edu```

```srun -p groupname -A groupname --time=2:00:00 --mem=100G --pty /bin/bash```

```module load mathematica_<version>```

```mathematica```
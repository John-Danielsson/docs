# Modules (Deprecated)

_NB: It is preferable to use Singularity to create containers instead instead of creating environmental modules. Here's [a link to the singularity section](singularity.md)._

## The Basics

Different users may use different versions of some software, so Hyak has the module command which allows you to use a specific version of some software.

This command will load the module for R version 3.2.0:
```module load r_3.2.0```

After the above command, you can issue commands such as R, Rscript etc.

This command shows all the available modules:
```module avail```

This command shows your currently loaded modules:
```module list```

This command unloads the module for R version 3.2.0:
```module unload r_3.2.0```

_NB: The modules starting with the prefix contrib/ are created by Hyak users and are not maintained by the Hyak team._

## For Advanced Users

### Examples of Module Files

On Ikt, you can see module files at:
```/sw/Modules/modulefiles```

On Mox, you can see module files at:
```/sw/modules-1.775/modulefiles```

### How to Create Your Own Modules

Suppose version 1.0 of your executable program "myprogram" is in the directory /sw/contrib/myprogramdir/1.0. You can create your own modules for use by your group "groupname".

(1) ```mkdir /sw/modules-1.775/modulefiles/contrib/myprogram_groupname```

(2) ```cd /sw/modules-1.775/modulefiles/contrib/myprogram_groupname```

(3) Edit a file with the name 1.0.

(4) Put below  contents in the file from step 3.

```#%Module1.0#############################```

```proc ModulesHelp { } {```

```puts stderr "\tAdds myprogram 1.0 to the PATH."```

```}```

```module-whatis "Adds myprogram 1.0 to the PATH."```

```prepend-path    PATH            /sw/contrib/myprogramdir/1.0```

```######################################```

### Using your own module:

After doing the steps in the previous section, you can issue the below command to load your module.

(1) Issue this command to verify that your module contrib/myprogram_groupname/1.0 shows up.
    ```module avail contrib```

(2) Issue this command to load your module.
    ```module load contrib/myprogram_groupname/1.0```

(3) Issue this command to verify that your module is loaded.
    ```module list```

(4) Issue this command to verify that you can run myprogram.
    ```myprogram```

(5) Issue this command to unload your module.
    ```module unload contrib/myprogram_groupname/1.0```

The below links have more information.
https://modules.readthedocs.io/en/stable/module.html
https://modules.readthedocs.io/en/stable/modulefile.html
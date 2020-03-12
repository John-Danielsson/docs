# Data Management

## Hyak File Systems

### GSCRATCH

Each Hyak group has a shared scratch directory under /gscratch which is shared among all Hyak nodes. If your group is "hyak-groupname", your directory would be /gscratch/groupname. In most situations on Hyak, you should use your group's /gscratch directory. Some situations might benefit from the use of node-local scratch space described below. **GSCRATCH is not backed up.**

Side note: Some Hyak users have additional shared scratch space dedicated to your projects. Ask your PI for more information about this.

Shared scratch directories are configured "setgid". This ensures any files you create within inherit the group association of the parent directory.

File modification time (mtime) is only periodically updated and file access time (atime) is disabled on the /gscratch filesystem. Both these configurations are to improve the performance on /gscratch. Running find with any of the time options such as mtime or atime may not produce expected results.

### Quotas
Each hyak group is assigned a filesystem quota based on the number of nodes it owns. Quotas in the shared scratch space are managed based on file group association. Check your quotas with the ```mmlsquota``` command:

MOX: ```mmlsquota -j mygroup  --block-size G gscratch```

IKT: ```mmlsquota -j hyak-mygroup  --block-size G suppscr```

Usage reporting is written to each group's space hourly in a file at the root of the directory called usage_report.txt. Reports can also be sent by e-mail weekly (Monday morning) and daily when your group is over their quota. If you're interested in e-mail reporting, please contact us.

### Home Directories
Each Hyak user also has a home directory which is shared among al Hyak nodes. Quotas are set to 10GB with a limit of 5,000 files. Hyak home directories are intended for files you want to keep completely private (ssh keys) or which are completely unique to you (your login scripts). They are not for storing big code source trees or doing any computation. The home filesystem is small and slow, so please limit your work to the scratch filesystems. **Home directories are not backed up.**

Check your /home quota with the mmlsquota command:

MOX: ```mmlsquota --block-size G gscratch:home```

IKT: ```mmlsquota --block-size G suppscr:home```

### Node Local Scratch

Each node in Hyak has an /scr filesystem, typically with ~100GB of space. These filesystems are accessible only to the local node and are not shared with any other Hyak nodes. This is available to you for use in cases where your application performance is limited by the shared scratch. For example, mpiBLAST  partitions a reference database and distributes it among the nodes participating in a calculation to achieve better I/O scaling. The local scratch disk is cleaned up after each job completes. If you would like to keep data on the local scratch disk of your nodes, create a directory with group name and put your data there (if your UNIX group is hyak-groupname, you would create /scr/groupname). **Node local scratch filesystems are not backed up.**

### Transferring Files (all commands executed on the remote host)

**Into** Hyak
- Via scp, e.g. command line:
    - ```scp filename user@ikt.hyak.uw.edu:path/to/destination/directory```
- Via tar, e.g. command line:
    - ```tar -czf - local_directory | ssh user@ikt.hyak.uw.edu 'tar -xvzf -'```
- Via tar, e.g. command line through ssh channel (see Logging In)
    - ```tar -czf - local_directory | ssh -S /tmp/$USER-hyak-socket ikt.uw.edu "tar -xvzf -"```
- Via bbcp
    - ```bbcp mydatafile ikt1.hyak.uw.edu:/gscratch/mygroup/myuserid```
    - ```bbcp mydatafile ikt2.hyak.uw.edu:/gscratch/mygroup/myuserid```

**Out of** Hyak
- Via scp, to local working directory
    - ```scp user@hyak.washington.edu:path/to/file .```
- Via tar
    - ```ssh user@hyak.washington.edu '(cd path/to/destination/directory; tar -czf -)' | tar -xvzf -```
- Via tar, e.g. command line through ssh channel
    - ```ssh -S /tmp/$USER-hyak-socket ikt.hyak.uw.edu "tar -czf /path/to/remote/directory" | tar -xvzf -```

### Notes On I/O Performance

You will see substantially better I/O performance by using I/O buffers 4MB or larger and by avoiding lots of open/close operations on files. Not following these guidelines will result in poor I/O performance for all Hyak users, not just you. The shared storage on Hyak is a limited resource in capacity and performance: Please try to be a good neighbor.

### Remote Filesystems - lolo

Groups participating in Hyak have access to the lolo archive scalable storage services.

The Archive filesystem is intended for long term storage of data that changes infrequently, if ever. It is a tape-based system with a small disk buffer to improve write performance. It should **NEVER** be used for **ANY** sort of interactive access. It is equally important that you **NEVER** store thousands of **SMALL FILES**. Instead, collect directories of small files into tar files **BEFORE** transferring them to the Archive. The Archive quota limits you to 10.000 files/TB - an average file size of 100MB. This means if you store even a few tiny files, the remaining files must be very large - typically several GB in size.

The filesystem will perform best when used with large (> 1MB) files. For this and other reasons, we strongly encourage you to use tar to combine collections of small files into tar files before transferring them to the Archive filesystem for safekeeping. Be sure to read the section on Quotas below: limits are in place on the capacity each group can use and on the number of files.

Hyak groups have disk space on lolo at /archive/yourgroupname. 
However, hyak (Ikt and Mox) and lolo are separate systems. Hyak users can transfer data between them by using SFTP or SCP from the login nodes. See below link for more details on how to transfer files from hyak to lolo.
Hyak lolo file transfer

### Enabling lolo Access

Enable lolo access at the below link:
http://uwnetid.washington.edu/manage/?service

### Notes on Using the Archive

Files written to the archive are cached on disk before being migrated to tape. Once migrated, a backup is made on tapes stored in a separate data center. Copy data to or from the archive using the same commands as you for any other filesystem. For the Archive to function well, however, there are some important guidelines:
- The Archive should **NEVER** be used for **ANY** sort of interactive access
    - This means it's OK to recall the files associated with a project a few months or years after copying them to the Archive. 
    - It is **NOT OK** to recall the same files from the Archive on a weekly basis.
- The Archive should **NEVER** store thousands of **SMALL FILES**.
    - Instead, collect directories of small files into tar files BEFORE transferring them to the Archive - like so:
        - ```$ tar -cvf subdir.tar subdir```

After this you can use SFTP or SCP to transfer subdir.tar to lolo.

More details on that:

When interacting with the Archive filesystem it is very important to avoid unnecessarily recalling files from tape. In most cases, opening an archived file requires recalling it from tape. This triggers a sequence of events:

- The tape library fetches the appropriate tape and loads it into a tape drive (< 2 minutes)
- The tape drive seeks to the location of your file (< 2 minutes)
- The tape drive begins streaming your file to the disk cache (> 100MBs for large files)

As you can see, this is a heavyweight operation and should be avoided when possible. For example, recalling a file simply for appending is almost never a good thing. Instead, we encourage everyone to make "whole file" copies (use the "W" or "-whole-file" flag with rsync). This will replace the earlier version of the file with the new version without triggering a recall from tape. For example, to update your archive of a very important directory from your laptop (```$ rsync -av --whole-file ImportantDir lolo.washington.edu:/archive/hyak/groupname/```) where "groupname" is the name of your Hyak group.

Anytime you use rsync with the Archive filesystem it is very important to use the "--whole-file" argument to prevent rsync from recalling previous versions of each file from tape for comparison. You can rerun this command anytime you want to update the archive copy. It will not delete any files from the archive and will replace any previously archived versions of files with the updated versions.

Files deleted from the Archive persist on tape for some time. This is accomplished by two means. First, it is a nearly unavoidable consequence of working with tape. When you issue a delete command, the filesystem metadata is updated to reflect the new state of the file (deleted), but the blocks occupied by that file on tape are not immediately reclaimed. Periodically, an automatic reclamation process transfers data from tapes with deleted file "holes" to new tapes, returning the tapes with the deleted file blocks to the empty tape pool for reuse. Second, we back up the migration tape copy to a second tape copy. The backup retention policy is to preserve all files for at least one month beyond their date of deletion. This means you can be confident that when you replace a file in-place (as with the rsync example, above), the previous version of the file will be recoverable if something goes wrong.

Be aware of the quota on the number of files you can store in the Archive: 10,000 files per TB. This works out to an average file size of 100MB If you are using the Archive to store directories containing many small files, consider using tar to combine these files into one big file before transferring them to the Archive. Not only will this help manage your quota, but large files ensure much better tape drive performance, resulting in quicker recall of your data when you need it.

**SSH Access**

You may use ssh to access lolo from Hyak, Campus, or the Internet. 

**Quotas**

Groups are entitled to at about 450GB of archive space for each node they operate in Hyak. Quotas are applied based on the contents of each group’s directory.  

When logged into lolo, you can report your group’s quotas with mmlsquota.
```-bash-3.2$ mmlsquota -j hyak-groupname archive```
where groupname is your group’s name.

Quotas are also set on the number of files you can have. The limit is 10,000 files per TB in the Archive.

### Purchasing Additional Capacity

lolo is a UW-IT service to the UW research community, not just Hyak. Details and pricing are available directly from the [UW-IT Service Catalog](https://uw.service-now.com/sp?id=sc_entry&sys_id=77750042db1e73c037ae9ec6db961994).

### Performance

We have measured transfers from peers on es.net as fast as 650MBs. Transfers with Teragrid sites have typically been in the 50MBs range (we’re looking into it). Copying a single 100GB file between lolo and /gscratch takes less than seven minutes (~285MBs). Aggregate transfer rates are higher.

### Cloud Storage

We provide the Google gsutil tool for transferring data from Google Cloud Storage and Amazon S3. If you would like more details on its usage, please see the [Google documentation](https://cloud.google.com/storage/docs/gsutil).
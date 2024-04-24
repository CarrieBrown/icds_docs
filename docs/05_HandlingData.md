
# Handling Data




## Available Filesystems and Quotas

Roar Collab (RC) offers several file storage options for users, each with their own quotas and data retention policies. The multiple options are available for users to optimize their workflows.


### Storage Information

| Storage | Location | Space Quota | Files Quota | Backup Policy | Use Case |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Home | /storage/home | 16 GB | 500,000 files | Daily snapshot | Configuration files |
| Work | /storage/work | 128 GB | 1 million files | Daily snapshot | Primary user-level data storage |
| Scratch | /scratch | None | 1 million files | No Backup, Files purged after 30 days | Temporary, unimportant files |
| Group | /storage/group | Allocation-dependent | 1 million files per TB allocated | Daily snapshot | Primary storage for shared data |

Home should primarily be used for configuration files and should not be used as a primary storage location for data. Work should be used as the primary personal data storage location. Scratch should be used for temporary files and for reading and writing large data files.

To provide a user with access to a paid group storage allocation, the owner of the storage allocation should submit a request to icds@psu.edu to add the user to their *_collab group.


### Check Usage

To check storage usage against the storage quotas, run the following command on RC:
```
$ check_storage_quotas
```

The ouputs generated by these scripts are not generated in real-time, but the underlying quota information is updated several times per day. If you remove many files, for instance, the updates to your usage will not be reflected in the outputs until the next update period.

For a real-time look into your memory usage for a particular storage location, navigate to the storage location and run the following command:
```
$ du -sch .[!.]* * | sort -h
```

For a real-time look into the number of files in a storage location, navigate to the storage location and run the following command:
```
$ find . -type f | wc -l
```


### Managing Large Configuration Files

Home is the primary location for configuration files, and many software packages will automatically place configuration files in this location. Sometimes, these configuration files can grow in size such that the Home directory approaches its storage quota limit. If this issue occurs, it is simple to move the configuration files from Home to Work and place a link in Home that points to the new location of the configuration files in Work.

For instance, Anaconda stores its configuration files in *~/.conda* by default, and this directory often grows to multiple GBs in size, consequently using a significant portion of the Home directory's allocated memory. The *~/.conda* directory can be moved to Work and can be replaced by a link in Home that points to the new location. This can be carried out with the following commands:
```
$ mv ~/.conda /storage/work/$(whoami)
$ ln -s /storage/work/$(whoami)/.conda ~/.conda
```


## File Transfers

Globus is a web-based file transfer tool. With Globus, users can easily, reliably, and securely transfer data to and from RC. The recommended file transfer method for RC is [Globus](https://www.globus.org/). The Files tab on the [RC Portal](https://rcportal.hpc.psu.edu/) is also very convenient for transferring files. For small-scale file transfers to/from RC, the submit nodes (hostname **submit.hpc.psu.edu**) can be used. When specifying a file location, it is best to use the full file path.


### Globus

It is recommended to use [Globus](https://www.globus.org/) for file transfers, especially for files that are multiple GBs in size or larger. Also, if issues due to an unreliable connection arise, transferring via Globus may be a good option. Globus is a file transfer tool that automates the activity of managing file transfers, such as montitoring performance, retrying failed transfers, recovering from faults automatically whenever possible, and reporting status.

Globus endpoints must be installed on both the source and destination systems. RC has Globus endpoints available.
```
Roar Collab Endpoint:
PennState_ICDS_RC

Roar Collab Archive Endpoint:
Archive_PennState_ICDS
```

To transfer files with Globus, visit the [Globus website](https://www.globus.org/) and log in as a Penn State user with your Penn State access account. Select the File Manager tab on the left side of the Globus web interface and select the source and destination endpoints. The endpoints may require an additional login. The files and locations for the transfer can be selected graphically, and the transfer can be initiated by selecting *Start* above the source endpoint file preview window. The transfer will be handled by Globus, and typically, successful completion of the transfer will generate an email to your Penn State email.

Users can also download files from RC to their local device or upload files directly from their local device to RC using simple web interface operations. To download a file, right-click the file and select Download. To upload a file, select Upload from the Pane 1 Menu on the right.


### Files Tab on RC Portal

The Files tab on the RC Portal offers a very intuitive interface for file management. Files can be moved, edited, uploaded, and downloaded with relative ease using this utility. Users should limit the use of the RC Portal file manager utility to dealing with small files only.


### scp

Users may use the `scp` command to transfer files to and from RC. It is typically practical to zip files together when transferring many files at once. In a terminal session, the `scp` command can be used to transfer files in the following way:
```
$ scp [options] <source-user-id>@<source-host>[:<file-location>] <destination-user-id>@<destination-host>[:<file-location>]
```

Some examples will make the usage more clear. The two locations in this example are the directory **/home/abc** on a local laptop device and the user scratch space on RC. If a file named *local.file* in **/home/abc** is to be transferred to a user's scratch space, the user should run the following command from a terminal session on the local laptop:
```
# Transfer to Roar Collab scratch space
$ scp /home/abc/local.file <userid>@submit.hpc.psu.edu:/scratch/<userid>
```

Alternatively, if the user navigates to the **/home/abc** location on their local laptop, the command can be slightly simplified to
```
# Transfer to Roar Collab scratch space
$ scp local.file <userid>@submit.hpc.psu.edu:/scratch/<userid>
```

If a directory named *datadir* located on the user's RC scratch space is to be transferred to the local laptop's **/home/abc** directory, the user can run the following from a terminal session on the local laptop:
```
# Transfer from Roar Collab scratch space
$ scp -r <userid>@submit.hpc.psu.edu:/scratch/<userid> /home/abc/
```

Note that since a directory is being transferred, the `-r` option must be used for the `scp` command so both the directory and its contents are transferred. If the user navigates the terminal to the **/home/abc** directory to conduct the transfer, then the **/home/abc/** in the above commands can be replaced with a single period **.** to denote the *current working directory*.
```
# Transfer from Roar Collab scratch space
$ scp -r <userid>@submit.hpc.psu.edu:/scratch/<userid> .
```


### sftp

The `sftp` command can also be used to transfer files and is more useful when transferring multiple smaller files in a piecemeal fashion. This method allows for interactive navigation on the remote connection. From a local device, an `sftp` connection can be made with
```
$ sftp <userid>@<remote-host>[:<location>]
```

To spawn an `sftp` connection on RC, use
```
$ sftp <userid>@submit.hpc.psu.edu
```

After the `sftp` connection is made, navigational commands (i.e. `ls`, `cd`, etc) are performed on the remote connection normally, while navigational commands are performed on the local connection by appending the letter **l** (lowercase L) to the commands (i.e. `lls`, `lcd`, etc). Files are transferred from the local device to the remote device using the `put <filename>` command, and files are transferred from the remote device to the local device using the `get <filename>` command. The connection is terminated with the `exit` command.


### rsync

Yet another file transfer option is `rsync`. The `rsync` tool is widely used for backups and mirroring and as an improved copy command for everyday use. The `rsync` command takes the form
```
$ rsync [options] <source-user-id>@<source-host>[:<file-location>] <destination-user-id>@<destination-host>[:<file-location>]
```

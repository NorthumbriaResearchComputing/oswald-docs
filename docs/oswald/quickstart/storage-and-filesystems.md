# Storage and Filesystems

## Available Filesystems

Like all HPC clusters, Oswald provides primary, scratch, and high-performance storage areas for you and applications to use:

| Storage Type             | Location     | Node(s) Available On              | Size                                                        | Storage Type (speed)                   | Quotas |
|--------------------------|--------------|-----------------------------------|-------------------------------------------------------------|----------------------------------------|--------|
| Main storage             | /home        | Head, Compute, GPU, Visualisation | 185TB                                                       | HDD (slow)                             | None   |
| Scratch storage          | /local       | Compute                           | 72GB on compute nodes 1-25;<br>184GB on compute nodes 26-32 | SSD (fast)                             | None   |
| High-performance storage | /lustre/lzfs | Head, Compute, GPU, Visualisation | 88TB                                                        | Lustre (very fast for parallel access) | None   |

Important Notes:

- Each user has their own subdirectory on the main and Lustre filesystems, eg. `/home/bob` (called the user's "home directory") and `/lustre/lzfs/bob`, to ensure different users do not accidentally interfere with each other's files. Users should not, and in some cases cannot, make files or directories directly in `/home` or `/lustre/lzfs`.
- All users can access the home directories of all other users, though files within each user's home directory are private when created. To share a file with other Oswald users, the owner of the file must set read and navigate permissions for 'other users' on their home directory and any parent directories of the file (by using, eg. `chmod o+rx dir_name`), then set the relevant permissions on the file itself (by using, eg. `chmod o+rwx some_script.py`).
- There are currently no per-user quotas for user storage, though some restrictions are expected to be introduced in the future.
- No backups are kept of any data on any filesystem - users are expected to keep their own backups of any software and important input or generated data that they store on the cluster.
- When using `/local` scratch storage on compute nodes, users are responsible for cleaning up after every job to avoid it becoming full.

??? todo
    - Is `/tmp` accessible by all, some, or no applications?
    - On which nodes can it be accessed (head, compute, GPU, visualisation)?
    - If it's not available for some apps and/or on some nodes, would some apps that need to use it have problems running?

??? todo
    Should each user/job use a subdirectory of `/local` to avoid usage conflicts? (eg. two jobs using a file with the same name, one job deleting a file being used by another job, etc.)

## Transferring Files

Oswald supports both `scp` and `sftp` for transferring files between your machine and Oswald. Clients for these are available for various operating systems:

- **Unix/Linux/Mac**: The `scp` and `sftp` programs are available directly. Use whichever you find most convenient with:

    ```
    scp <file-name> <username>@oswald:<destination-directory>
    ```

    or

    ```
    sftp <username>@oswald
    ```

    See `man scp` and `man sftp` for more information on how to use these programs.

- **Windows**: Various applications exist that support either `scp`, `sftp`, or both. Common applications include:
    - [WinSCP](https://winscp.net/eng/index.php)
    - [Filezilla](https://filezilla-project.org/)
    - [MobaXterm](https://mobaxterm.mobatek.net)

    If none of this software is installed on your machine:
    
    - If using your own machine, you can download and install the application of your choice from the pages linked above.
    - If using a university machine, log a ticket with the IT Helpline to have the needed software set up on your machine.

    Refer to the documentation for these applications for how to use them to transfer files. The 'server address' you need to connect to is `oswald`.

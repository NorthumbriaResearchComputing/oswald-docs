# Storage and Filesystems

## Transferring Files

Oswald supports both `scp` and `sftp` for transferring files. Clients for these are available for various operating systems:

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

## Available Filesystems

- /home
- /tmp + $TMPDIR ???
- Lustre (where???)
- Scratch ???
- etc.

Capabilities and policies:

- speed, parallel?, quotas, backup, etc.

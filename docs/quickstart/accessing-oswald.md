# Accessing Oswald

## Getting an Account

To access the oswald cluster you need to have an account set up by the cluster system administrators. Log a ticket with the IT Helpline to request this and you will be provided with a username and password by email (your 'registration email').

## Logging In

!!! note
    Throughout the following documentation, replace `<username>` with the username you were given in your registration email.

### SSH Clients

Like most supercomputers, Oswald allows you to log in using Secure Shell (SSH). SSH is available for various operating systems:

- **Unix/Linux**: The OpenSSH client is pre-installed on many Linux distributions. If the OpenSSH client is not installed on your system:
    - If using your own machine, you can install it with your distribution's package manager (eg. `sudo apt install openssh-client` on Ubuntu-based systems).
    - If using a university machine, log a ticket with the IT Helpline to have it set up on your machine.

    Once installed, log in using:
    
    ```
    ssh <username>@oswald
    ```

- **Mac**: The OpenSSH client is pre-installed on most Macs. Log in using:

    ```
    ssh <username>@oswald
    ```

- **Windows**: Multiple applications for Windows allow using SSH, including:
    - [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty)
    - [MobaXterm](https://mobaxterm.mobatek.net)

    If none of this software is installed on your machine:
    
    - If using your own machine, you can download and install the application of your choice from the pages linked above.
    - If using a university machine, log a ticket with the IT Helpline to have the needed software set up on your machine.

    Refer to the documentation for these applications for how to connect using them. The 'server address' you need to connect to is `oswald`.

### Security and Authentication

After running the above command (or attempting to log in through your SSH client application), you will be prompted for a password. You should use the password you received in your registration email.

The first time you log in, you may also be asked to accept the "host key" of Oswald's head node. You can accept this.

### Configuring SSH

When using the above Unix/Linux/Mac commands, having to repeat them every time you wish to log in or [transfer files](/quickstart/storage-and-filesystems) can become tedius. OpenSSH allows using a configuration file (usually located at `~/.ssh/ssh_config`) to save some of the information for future logins.

In that file, you can add the following:

```
Host oswald
  HostName oswald
  User <username>
```

After that, you can provide the server address (which usually looks like `<username>@oswald` as simply `oswald`), such as in:

```
ssh oswald
scp <file-name> oswald:<destination-directory>
sftp oswald
```

!!! note
    The configuration file is per-user on your machine - other users will not see your Oswald username unless you share the file.

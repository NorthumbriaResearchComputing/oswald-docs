# Software Environment and Modules

Most of the software you will use on Oswald is installed in what is called a “Modules Environment”.

On a traditional Unix/Linux system, software and libraries added by users are installed alongside operating system software and libraries in standard directories (such as `/bin`, `/sbin`, `/usr/bin`, `/usr/local/bin`, `/lib`, and `/usr/lib`). However, this makes it difficult to separate user and system software and install multiple different versions of the same software or library at the same time.

A modules environment provides the means to bundle each software or library package into a self contained module so that:

- System software/libraries are kept separate from user software/libraries.
- Different versions of the same software or library can be installed side-by-side.
- Each user can configure their own environment the way they need it.

## Using Modules

Managing software in a modules environment is done with the various sub-commands of the `module` command.

To know what software and libraries are currently in use in your environment, you can use the `module list` command. This will show something like:
```
$ module list
Currently Loaded Modulefiles:
 1) gcc/5.2.0   2) slurm/15.08.6
```

To use a particular piece of software or library, you must load the module for that software into your environment. This is done with `module load <module-name>`, replacing `<module-name>` with the name of the module for the software you want to use. A list of module names and the software they contain can be found on the [Software List](../software/software-list.md) page.

As an example, lets say you want to use version 16.0.4 of the Intel compilers. You can load the Intel compiler module using the `load` sub-command and then list the loaded modules:

```
$ module load intel/compiler/64/16.0.4/2016.4.258
$ module list
Currently Loaded Modulefiles:
 1) gcc/5.2.0
 2) slurm/15.08.6
 3) intel/compiler/64/16.0.4/2016.4.258
```

Now you can use the Intel Compilers, e.g. `ifort`, `icc`, `icpc`.

When you have finished using a module, you can remove it from your environment using `module unload <module-name>`. This command is mainly useful if you want to use a different version of a piece of software or library that does not allow multiple versions to be loaded at once, in which case you must unload the version you are currently using before loading the other version. All modules will automatically be unloaded when you log out.

Continuing on from the previous example, to unload the Intel compiler module:

```
$ module unload intel/compiler/64/16.0.4/2016.4.258
$ module list
Currently Loaded Modulefiles:
 1) gcc/5.2.0   2) slurm/15.08.6
```

You can unload all modules from your environment using `module purge`. For example:

```
$ module purge
$ module list
No Modulefiles Currently Loaded.
```

### Initial Modules

The initial set of modules loaded into your environment when you log in can be changed using the `module init*` sub-commands (see [Summary of Module Commands](#summary-of-module-commands) for details of each sub-command). Use these commands if you often load a particular module or set of modules to save you from manually loading them every time you log in.

## Summary of Module Commands

General:

| Command                       | Description                                                                     |
|-------------------------------|---------------------------------------------------------------------------------|
| `module avail`                | Lists all modules that are available to you to use.                             |
| `module list`                 | Lists all modules that are currently loaded.                                    |
| `module load <module-name>`   | Loads the module with the given name into your current environment.             |
| `module unload <module-name>` | Removes (unloads) the module with the given name from your current environment. |
| `module purge`                | Removes (unloads) all modules from your current environment.                    |

Initial Modules:

| Command                        | Description                                           |
|--------------------------------|-------------------------------------------------------|
| `module initadd <module-name>` | Automatically load the given module on login.         |
| `module initrm <module-name>`  | Stop automatically loading the given module on login. |
| `module initlist`              | List all modules currently being loaded on login.     |
| `module initclear`             | Stop automatically loading any modules on login.      |

## Oswald-Specific Software Information

The [Software List](../software/software-list.md) page contains a list of pages containing general information about the software and libraries that are commonly used on Oswald (eg. MPI). These pages also contain **specific information about using the software on Oswald**. It is recommended to check the pages on the software you want to use before you start using it on Oswald.

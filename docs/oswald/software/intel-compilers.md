# Intel Compilers

The Intel compilers are available for use on Oswald.

??? todo
    What packages (exact names) provide the Intel compilers and any related tools?

!!! warning
    Modules relating to intel compilers with `mic` in their name are for use with CPUs that use the Intel MIC architecture, such as the Xeon Phi processors. At the time of writing, Oswald has no nodes of this type in the cluster, so **do not** use those modules. Using them will lead to unspecified behaviour, possibly including cluster instability.

The compilers for the languages supported on Oswald (though others may work) are as follows:

| Description          | Command  |
|----------------------|----------|
| C compiler           | icc      |
| C++ compiler         | icpc     |
| Fortran compiler     | ifort    |
| MPI C compiler       | mpiicc   |
| MPI C++ compiler     | mpiicpc  |
| MPI Fortran compiler | mpiifort |

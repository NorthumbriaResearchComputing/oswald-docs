# MPI

MPI is a communication protocol for programming parallel computers. It allows the compilation of code which can be used over many processors over (possibly) many compute nodes at the same time. Most MPI implementations consist of a specific set of routines directly callable from programming languages such as C, C++, and Fortran, as well as any other programming languages able to interface with such libraries, including C#, Java, and Python. The advantages of MPI over older message passing libraries are:

- **Portability**: MPI has been implemented for almost every distributed memory architecture.
- **Speed**: Each implementation is in principle optimized for the hardware on which it runs.

There are 4 MPI-2 implementations available on Oswald:

- MPICH2 (`mpich2`)
- MVAPICH2 (`mvapich2`)
- OpenMPI (`open-mpi`)
- Intel MPI (`intel-mpi`)

These libraries can be compiled with GCC, Open64, and Intel compilers.

!!! warning "Important Note"
    As Oswald uses an OmniPath (OPA) interconnect (100Gb/s) you should *only* use the Host Fabric Interface-compatible (HFI) libraries (those with the `-hfi` suffix), by loading the appropriate module. These have been compiled and optimised to use OmniPath. The other libraries will default to communicate over the management network which is much slower (1Gb/s) and can cause cluster instability.

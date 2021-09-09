# Application Development Tools

Oswald includes the [GNU](../software/gnu-compilers.md) and [Intel](../software/intel-compilers.md) compilers for software development. The GNU compilers are general compilers that produce executable files that can run on any computer architecture. The Intel compilers are designed and optimised for Intel architectures - executable files created by them can only run on Intel CPUs (which Oswald uses), but *may* be smaller and run faster than programs created from the same source code using the GNU compilers. See their individual pages for full details of the names of their modules and the commands they provide.

!!! warning
    While building an application, you must use the same compiler and libraries (whether pre-built/pre-installed, or written yourself) throughout the build. Mixing library versions or compilers (vendors or versions), including using libraries compiled with a different compiler/compiler version, may result in problems compiling your application, or runtime problems even if it compiles.

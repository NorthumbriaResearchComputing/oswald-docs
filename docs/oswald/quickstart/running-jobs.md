# Jobs

Work on the cluster is submitted as a job to a workload manager which allocates the job to one or more compute nodes, depending on the requested and available resources. Jobs are held in a job queue until sufficient resources are available on the cluster to run the job. Several workload managers exist for different supercomputers, such as PBSPro, Torque, SGE, and SLURM. Oswald uses the SLURM ('Simple Linux Utility for Resource Management') workload manager.

!!! warning
    Working on the head node is only permitted for setting up job scripts, submitting jobs, and editing and compiling source code. Though technically possible, HPC applications (whether pre-installed on Oswald, aquired from an external source, or written/compiled yourself) should **never** be run on the head node, even for testing. If you need to test your application or job, you should run it on the cluster's `debug` queue. See [Job Queues/Partitions and Limits](#job-queuespartitions-and-limits) for details.
    
    GUI applications also cannot be run on the head node, even for the valid purposes listed above. Using graphical applications for writing code or jobscripts, or creating small amounts of data, must either be done on your own machine (and the files copied onto oswald), or done on the visualisation node. For visualising your data/results, or other uses of GUI applications that involve manipulating large data sets, you should use the visualisation node. This avoids slow data transfers to and from Oswald and allows you to analyse very large data sets that you could not store on your own machine. For how to use the visualisation node, see [Visualisation Node](using-specialised-nodes.md#visualisation-node).

To create a job, a job script must be created and submitted to SLURM. SLURM job scripts may include a range of options to customise their behaviour. Once the job is submitted, it can be monitored and cancelled. The rest of this page describes each of these stages in more detail.

## Job Scripts

A job script is a shell script in which you specify the command(s) to run when the job is run, as well as setting various options for the job and other application-specific environment variables.

A job script has the following basic structure:

1. **Shebang Line**: A single line that specifies the shell to use (often `#!/bin/sh` or `#!/bin/bash`).
2. **SLURM Directives**: Optional lines that SLURM uses to set properties about the job, such as the number of nodes to use, the number of processor cores to use, memory requirements, etc. These can either have a 'long' format: `#SBATCH --<option>=<parameter>`, or a short format: `#SBATCH -<o> <parameter>` (where `<o>` is the option's single-character name). In either case, leave no space between the `#` and `SBATCH`, otherwise the directive will be treated as a comment and ignored. Many common options and what they do are listed [below](#slurm-directives).
3. **Setup**: Optional shell commands to load required modules and define
application-specific environment variables.
4. **Application Execution**: The shell command(s) to execute the application. This could include running software you have downloaded to assist in your research, an application you have written yourself, a script that uses pre-installed packages, or a combination of these.
5. **Garbage Collection**: The shell commands to clean up any temporary workfiles
generated during the job execution to leave the node(s) clean for the next job.

??? example
    Assume we have created a simple MPI-based C program:

    ```
    /* Hello World using MPI */

    // include the MPI header files
    #include <stdio.h>
    #include <stdlib.h>
    #include <mpi.h>

    int main(int argc, char **argv)
    {
        int rank, size, name_len;
        char hostname[20];

        // Initialise the MPI environment
        MPI_Init(&argc, &argv);

        // Get the number of processes
        MPI_Comm_size(MPI_COMM_WORLD, &size);

        // Get the rank of the process
        MPI_Comm_rank(MPI_COMM_WORLD, &rank);

        // Get the name of the node
        MPI_Get_processor_name(hostname, &name_len);

        // Print Hello World message from the process
        printf("Hello World from node %s rank %d out of %d\n", hostname, rank, size);

        //Finalize the MPI environment
        MPI_Finalize();
        return 0;
    }
    ```

    !!! note
        If you wish to follow along with this example, the program above is available for you to copy at `/home/EXAMPLES/HelloWorld/hello-mpi.c` on Oswald, or you can paste the above code into your favourite text editor and save it as a `.c` file.

    We will use the GNU MPI compiler with the OpenMPI libraries to compile and build the executable. The GNU MPI compiler is provided by the OpenMPI module. First we need to load the OpenMPI library module into our environment:

    ```
    module load openmpi/gcc/64/1.10.0-hfi
    ```

    !!! note
        We need to load the module with the `-hfi` suffix to use the OmniPath Interconnect. See [MPI](../software/mpi.md) for details.

    Next, compile the source code using the GNU MPI C compiler (`mpicc`):

    ```
    mpicc hello-mpi.c -o hello-mpi
    ```

    Finally, below is an example job script, named `slurm-hello-mpi.sh`, to run the 'Hello World' application on the cluster.

    ```sh
    #!/bin/sh

    # Configure the SBATCH directives for this job

    # Set Job Name to be displayed in the job queue
    #SBATCH --job-name=Hello-MPI

    # Specify where the output of this job will be stored
    # %j = job number
    #SBATCH --output=hello-mpi-%j.out

    # Set maximum wallclock time limit for this job
    # Time format = days-hours:minutes:seconds
    #SBATCH --time=0-00:30:00

    # Set number of nodes to use
    #SBATCH --nodes=2

    # Set number of tasks to run per node (tasks=cores/threads)
    #SBATCH --ntasks-per-node=10

    # Set the partition/queue to submit the job to
    #SBATCH --partition=24hour

    # Set email address to send email notifications
    #SBATCH --mail-user=your.email.address@here

    # Send an email when job has started, finished or aborted
    #SBATCH --mail-type=ALL

    # Setup
    # Load the modules required by this job
    module load slurm openmpi/gcc/64/1.10.2-hfi

    # Run the application using 20 process slots
    mpirun -n 20 hello-mpi
    ```

    !!! note
        The example job script above is available for you to copy at `/home/EXAMPLES/HelloWorld/slurm-hello-mpi.sh` on Oswald, or you can paste the above code into your favourite text editor and save it as a `.sh` file.

    The job script above is configured so that when it is submitted, it is submitted to the 24-hour queue (`--partition=24hour`). Then, once suffient resources are available on the cluster, SLURM will run the job over 2 nodes (`--nodes=2`) with 10 tasks per node (`--ntasks=10`), for a total of 20 process slots. The `mpirun` command will run the given application (`hello-mpi`) over the specified number of process slots (`-n 20`). Here, `20` makes it fill all 20 slots the job script requests.
    
    The job script also specifies that it will run for no more than 30 minutes (`--time=0-00:30:00`), and to notify you (`--mail-user=your.email.address@here`) when it has started and completed, or failed (`--mail-type=ALL`). Once it has completed, you will be able to access the output of the job in a file whose name is in the format `hello-mpi-<jobID>.out`, eg. `hello-mpi-20220.out`.

!!! note
    You should try to **use as many resources as you request** and only **request as many resources as you need** to avoid inefficiency in the job allocation system. Reserving resources you don't need only adds to the time other users have to wait for their jobs to start, as resources (CPU cores, RAM) that are reserved by your job but are not being used cannot be used by other jobs for as long as your job is running. It is rare that jobs will need to specify a RAM usage limit, but it is possible.
    
    Nodes are shared by default ('oversubscribed' in SLURM terminology), meaning different jobs can reserve their own reservable resources (CPU cores, RAM) *on the same node at the same time*. This can be overridden by specifying exclusive mode for a job using the `--exclusive` option to SBATCH in a job script, or in the `sbatch` command. Exclusive mode will make all nodes that job runs on reserved by that job, even if those nodes have spare resources (CPU cores, RAM) that are not being used by that job. As such, exclusive mode should only be used if the application the job is running requires it.

### SLURM Directives

The following is a list of common directives.

!!! note
    Lists of possible values for directives given here may not be comprehensive. A full list of all directives and possible parameter values can be seen by running `sbatch -help`, or running `sbatch -usage` for a more concise list.

| Option (long and short format)                                        | Description and Default Value                                                                                                                                                                     |
|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `#SBATCH --jobname=<jobname>`<br>`#SBATCH -J <jobname>`               | Set the name of the job.<br>*Default*: The name of the job script.                                                                                                                                |
| `#SBATCH --partition=<partition>`<br>`#SBATCH -p <partition>`         | Run the job in the specified partition/queue.<br>*Default*: `debug` (for Oswald).                                                                                                                 |
| `#SBATCH --nodes=<num-nodes>`<br>`#SBATCH -N <num-nodes>`             | Request the specified number of nodes.<br>*Default*: As many nodes as needed to provide for the number of tasks.                                                                                  |
| `#SBATCH --ntasks=<num-tasks>`<br>`#SBATCH -n <num-tasks>`            | Request the specified number of tasks (usually equivalent to CPU cores) per node.<br>*Default*: 1 task per node (so 1 task in total if `--nodes` is also not provided in the job script).         |
| `#SBATCH --time=<time>` \*<br>`#SBATCH -t <time>`                     | Request the maximum amount of time the task will need to run. This can be no more than the maximum time limit of the partition used. <br>*Default*: The maximum time limit of the partition used. |
| `#SBATCH --begin=<time>` \*                                           | Request the job to start at a specific time.<br>*Default*: As soon as the necessary resources become available.                                                                                   |
| `#SBATCH --workdir=<directory-path>`<br>`#SBATCH -D <directory-path>` | Set the working directory to run the script in.<br>*Default*: The working directory of the shell at the time it ran `sbatch` to submit the job.                                                   |
| `#SBATCH --error=<filename>`<br>`#SBATCH -e <filename>`               | Set the error log file name. This can be a relative path from your current directory.<br>*Default*: `slurm-<jobID>.out`                                                                           |
| `#SBATCH --output=<filename>`<br>`#SBATCH -o <filename>`              | Set the output log file name. This can be a relative path from your current directory.<br>*Default*: `slurm-<jobID>.out`                                                                          |
| `#SBATCH --mail-user=<user@address>`                                  | Set the email address for job notifications - use your email address.<br>*Default*: Do not notify.                                                                                                |
| `#SBATCH --mail-type=<BEGIN, END, FAIL, ALL>`                         | Set the type(s) of job notification to send - usually one of `BEGIN`, `END`, `FAIL`, or `ALL`.<br>*Default*: Do not notify.                                                                       |

\* Durations are given in the format `hours:minutes:seconds` or `days-hours:minutes:seconds`.

It is recommended that in every job script you at least specify the following:

- **Number of tasks**: This will depend on your job.
- **Maximum runtime**: This may require an educated guess or experience from previous job submissions. If in doubt, where you want to run several similar jobs, use the maximum for the relevant job queue for the first job submission, then use a reasonable value for the remaining job submissions based on how long the first one took to run. You can find out how long a job took to run by turning on email notifications for when the job starts and completes.
- **Partition**: Select a suitable partition based on the number of nodes, tasks, and time needed.

## Job Queues/Partitions and Limits

Oswald has several job queues ('partitions' in SLURM terminology), with different maximum 'wallclock' (actual/real) run times and maximum number of nodes per job, as in the table below:

| Queue/Partition Name | Max Number of Nodes | Max Wallclock Runtime |
|----------------------|---------------------|-----------------------|
| 120 hour             | 4                   | 120 hours / 5 days    |
| 72 hour              | 4                   | 72 hours / 3 days     |
| 48 hour              | 8                   | 48 hours / 2 days     |
| 24 hour              | 24                  | 24 hours / 1 day      |
| debug                | 1                   | 1 hour                |

When writing job scripts, you should specify to use the job queue with the shortest maximum wallclock time that is more than your job's maximum time. Similarly to setting appropriate resource limits (as mentioned above), this helps to avoid inefficiency in the job allocation system. When specifying the job queue to use (using `#SBATCH --partition`), do not put spaces in the queue name (eg. use `24hour`, not `24 hour`).

??? example
    **Minimal job script**: If you had a job that you know won't run for more than 60 hours (eg. it previously ran for 52:35:23) and only requires 80 tasks/cores (ie. a minimum of 3 nodes), then in your job script you could specify (as a minimum) something like:

    ```
    #!/bin/bash

    #SBATCH --ntasks 80
    #SBATCH --time 60:00:00
    #SBATCH --partition 72hour

    srun -n 80 your_desired_program_or_script
    ```

??? todo
    Check that this minimal example isn't against Oswald's policies on SLURM usage.

!!! note
    The `debug` queue is meant to be used as a quick check to see if your code and/or script are configured correctly. It is the default queue if you do not specify another queue using the `-p` or `--partition` #SBATCH directive in your job script, or on the command-line using the `--partition` option, like so:

    ```
    sbatch --partition=48hour slurm-hello-mpi.sh
    ```

    Normal jobs should **specify one of the other queues**, as appropriate for the job's requirements.

!!! warning
    If the requested number of nodes (`--nodes`) exceeds the partition's maximum number of nodes, or the requested time limit of a job script (`--time`) exceeds the partition's maximum time limit, then the job will be left in a PENDING state (possibly indefinitely), meaning it will not run.

## Submitting a Job

Once a job script is made it can be submitted to SLURM to be run on the cluster using the `sbatch` command:

```
$ sbatch <script-name>
```

It will then output a line that looks like:

```
Submitted batch job XXXXX
```

Where `XXXXX` is the job number assigned to your submitted job - you can use this to manage the job after it has been submitted.

??? example
    Continuing on from the previous example:

    ```
    $ sbatch slurm-hello-mpi.sh
    Submitted batch job 20220
    ```

## Monitoring a Job

You can monitor the progress of your job using the `squeue` command. This will state the following information about every job currently running on the cluster:

- Job ID
- Partition
- Job name
- Initiating user
- Current status - usually either pending (`PD`) or running (`R`)
- Wallclock running time - given in the format `days-hours:minutes:seconds`
- Number of nodes being used
- Exact list of nodes reserved - ranges are given in square brackets

??? example
    Assuming our job using the `hello-world-mpi.sh` job script was submitted with the job ID 20220, `squeue` might output the following (our job is the last job listed):

    ```
    $ squeue
    JOBID PARTITION     NAME     USER ST       TIME NODES NODELIST(REASON)
    20211    24hour slurm-he   jimbob PD       0:00     2 (Resources)
    20165   120hour MOS_Soil  leanneW  R 1-17:54:19     3 compute[022-024]
    20164   120hour SNI_Soil  leanneW  R 1-17:55:26     3 compute[019-021]
    20186   120hour Dots3D58  elfegoG  R    3:08:27     1 compute010
    20070   120hour MATLAB-p   jimbob  R 4-06:08:07     1 compute001
    20220    24hour Hello-MP   jimbob  R       0:01     2 compute[016-017]
    ```

    This shows our job has been running on 2 nodes (`compute016` and `compute017`) for 1 second. Note that job 20211 is being held in a pending state (`PD`) as the requested resources are not available - the job will start running once the requested resources are available.

You will be sent an email when your job starts/ends/fails according to the setting of the `--mail-user` and `--mail-type` #SBATCH directives in your job script.

!!! tip
    This feature is very useful when you submit a large job which may take days to complete - you can logout, leaving your job running, and return when you receive the email that your job has finished, hopefully successfully.

??? example
    In the 'Hello World' example, the emails will be sent almost simultaneously, as the job completes almost instantly (it is only making a file containing "Hello World from node X rank Y out of Z" 20 times, after all!).

## Job Output

When the job has finished, the job details will disappear from the queue (and output of `squeue`) and you can then examine the output file produced (if one is produced by your job). The file will be named whatever you specified in the `#SBATCH --output <filename.log>` directive, and will be located in your current directory. If you have logged out and logged back in since submitting the job, the file will be located in whichever directory was your current directory at the time you submitted the job with `sbatch`.

??? example
    The output file for the 'Hello World' job is `hello-mpi-20220.out`. As we expect it to be short, we can inspect it with the `cat` command:

    ```
    $ cat hello-mpi-20220.out
    Hello World from node compute016 process 4 out of 20
    Hello World from node compute016 process 5 out of 20
    Hello World from node compute016 process 6 out of 20
    Hello World from node compute016 process 8 out of 20
    Hello World from node compute016 process 9 out of 20
    Hello World from node compute016 process 2 out of 20
    Hello World from node compute016 process 3 out of 20
    Hello World from node compute017 process 14 out of 20
    Hello World from node compute016 process 0 out of 20
    Hello World from node compute016 process 1 out of 20
    Hello World from node compute016 process 7 out of 20
    Hello World from node compute017 process 15 out of 20
    Hello World from node compute017 process 16 out of 20
    Hello World from node compute017 process 18 out of 20
    Hello World from node compute017 process 19 out of 20
    Hello World from node compute017 process 10 out of 20
    Hello World from node compute017 process 12 out of 20
    Hello World from node compute017 process 11 out of 20
    Hello World from node compute017 process 13 out of 20
    Hello World from node compute017 process 17 out of 20
    ```

    !!! note
        As this is an MPI application, the output file is added to by each of the processes/tasks running on the requested nodes independently of one another (i.e. no process/task depends waits for any other process/task to output its line). As such, the output of this job probably won't be in sequential order - the messages appear in the order in which the processes/tasks run and print the text (which will appear to be almost arbitrary after the job has started).

## Cancelling a Job

You may realise after submitting a job that something is wrong with it, eg. you put it on the wrong queue, set the wrong number of cores, or otherwise misconfigured parameters, so you need to cancel the job. You can cancel a job using the `scancel` command, giving the job ID of the job you wish to cancel:

```
scancel <jobID>
```

??? example
    If we wanted to cancel the 'Hello World' job with ID 20220, we would run:

    ```
    scancel 20220
    ```

!!! note
    You can only cancel your own jobs. System administrators can cancel any job.

## More Information

More information about the SLURM workload manager and SLURM commands can be found on the [SLURM docs](https://slurm.schedmd.com/), or in the relevant man pages (eg. `man sbatch`).

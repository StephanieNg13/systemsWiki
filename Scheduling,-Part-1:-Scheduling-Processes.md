## Scheduling


# How is scheduling measured?
Scheduling effects the performance of the system, specifically the *latency* and *throughput* of the system. The throughput might be measured by a system value, for example number of bytes written per second, or number of small processes that can complete per unit time, or using a higher level of abstraction for example number of customer records processed per minute. The latency might be measured by the response time (elapse time before a process can start to send a response) or total wait time or turn around time (the elapsed time to complete a task). Different schedulers offer different optimization trade-offs that may or may not be appropriate to desired use. For example 'shortest-job-first' will minimize total wait time across all jobs but in interactive (UI) environments it would be preferable to minimize response time (at the expense of some throughput).

# What are some well known scheduling algorithms?

We will discuss four simple scheduling algorithms, Shortest Job First, First Come First Served, Priority and Round Robin.

* Shortest Job First (SJF)

The next process to be scheduled will be the process with the shortest total CPU time required. One disadvantage of this scheduler is that it needs to be clairvoyant. 

* First Come First Served (FCFS)

Processes are scheduled in the order of arrival i.e. the ready queue is a simple FIFO (first in first out) queue.

* Priority

Processes are scheduled in the order of priority value. For example a navigation process might be more important to execute than a logging process.

* Round Robin (RR).

Processes are scheduled in order of their arrival in the ready queue. However after a small time step a running process will be forcibly removed from the running state and placed back on the ready queue. This ensures that a long-running process can not starve all other processes from running.
The maximum amount of time that a process can execute before being returned to the ready queue is called the time quanta. In the limit of large time quanta (where the time quanta is longer than the running time of all processes) round robin will be equivalent to FCFS.

# Which schedulers suffer from starvation?
Any scheduler that uses a form of prioritization can result in starvation because earlier processes may never be scheduled to run (assigned a CPU). For example with SJF, longer jobs may never be scheduled if the system continues to have many short jobs to schedule.

For more information see
https://en.wikipedia.org/wiki/Scheduling_(computing)#Types_of_operating_system_schedulers

# Why might a process be placed on the ready queue?

A process is placed on the ready queue when it is able to use a CPU. Some examples include:
** A process was blocked waiting for a `read` from storage or socket to complete and data is now available.
** A new process has been created and is ready to start.
** A process was blocked on a synchronization primitive (condition variable, semaphore, mutex lock) but is now able to continue.
** A process is blocked but a signal has been delivered and a signal handler needs to run.

Similar examples can be generated when considering threads.

# What is 'wait time'? 

Wait time is the *total* wait time i.e. the total time that a process is on the ready queue. A common mistake is to believe it is only the initial waiting time in the ready queue.

If a CPU intensive process with no I/O takes 7 minutes of CPU time to complete but required 9 minutes of wall-clock time to complete we can conclude that it was placed on the ready-queue for 2 minutes. For those 2 minutes the process was ready to run but had no CPU assigned. It does not matter when the job was waiting, the wait time is 2 minutes.

If  `Tstart` and `Tend` are the start and end wall-clock times of the process and `run_time` is the total amount of CPU time required then,

`wait_time  = (Tend - Tstart) - run_time`


# What is the convoy effect?


# For more information see the lecture notes and wikipedia-

* https://subversion.ews.illinois.edu/svn/sp15-cs241/_shared/lectures/  )

* https://en.wikipedia.org/wiki/Scheduling_(computing)

# Linux Scheduling
As of February 2016, Linux by default uses the *Completely Fair Scheduler* for CPU scheduling and the Budget Fair Scheduling "BFQ" for I/O scheduling. Appropriate scheduling can have a significant impact on throughput and latency. Latency is particularly important for interactive and soft-real time applications such as audio and video streaming. See the discussion and comparative benchmarks here [https://lkml.org/lkml/2014/5/27/314] for more information.


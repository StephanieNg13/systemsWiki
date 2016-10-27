# What are some well known scheduling algorithms?

We will discuss four simple scheduling algorithms, Shortest Job First, First Come First Served, Priority and Round Robin.

# Shortest Job First (SJF)

![](http://i.imgur.com/jGLvjqT.png)

The next process to be scheduled will be the process with the shortest total CPU time required. One disadvantage of this scheduler is that it needs to be clairvoyant. Note the SJF is not shortest _remaining_ time; processes are ordered by their total CPU needs not the remaining CPU need.

SJF appears in both preemptive and non-preemptive versions. Preemptive SJF has the shortest total wait time when summed over all processes that have a known arrival time and execution time.

Technical Note: A realistic SJF implementation would not use the total execution time of the process but the burst time (the total CPU time including future computational execution before the process will no longer be ready to run). The expected burst time can be estimated by using an exponentially decaying weighted rolling average based on the previous burst time but for this exposition we will simplify this discussion to use the total running time of the process as a proxy for the burst time.

# First Come First Served (FCFS)

Processes are scheduled in the order of arrival. One advantage of FCFS is that scheduling algorithm is simple: the ready queue is a just a FIFO (first in first out) queue.
FCFS suffers from the Convoy effect (see below).

# Priority

Processes are scheduled in the order of priority value. For example a navigation process might be more important to execute than a logging process.

# Round Robin (RR)

Processes are scheduled in order of their arrival in the ready queue. However after a small time step a running process will be forcibly removed from the running state and placed back on the ready queue. This ensures that a long-running process can not starve all other processes from running.
The maximum amount of time that a process can execute before being returned to the ready queue is called the time quanta. In the limit of large time quanta (where the time quanta is longer than the running time of all processes) round robin will be equivalent to FCFS.

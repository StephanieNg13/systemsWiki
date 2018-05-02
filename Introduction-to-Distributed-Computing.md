An introduction to Distributed Computing will be added to Fall 2018

# What is Distributed Computing?

Distributed computing studies efficient use of physically separate computational systems. 

Simple distributed computing problems might span just a handful machines or even millions of platforms. For example, you might run your interactive 3D simulation on a remote machine, store your world data on another server and wonder if you can improve the latency of your Virtual Reality experience.  Or you might create a highly parallel search algorithm, where each compute node independently models and searches for the lowest energy configuration of a protein molecule, and then wonder how to best use the world's spare computation and network capacity to run your search algorithm. Though these are examples of computing span multiple machines, the field of "Distributed Computing" is even more complex and interesting than these "simple" problems.

Distributed Computing is interesting for the very same reasons that make it difficult! So far in this system programming book we've already introduced three core areas - concurrency, networking and data flow, fault-tolerant computing and communication - that are foundational in many Distributed Computing problems. The _distributed_ part however also means we need to study messaging, coordination and synchronicity (timing) between our systems and how we can best partition the computation and data of a particular kind of computing problem across multiple systems that are physically separated. We can also develop and evaluate algorithms and protocols that use distributed computing resources efficiently (or not!). For this we will also need to decide what are the important details that we need to include in our theoretical mode of our distributed system.



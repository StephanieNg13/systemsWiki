# Overview

The next section deals with what happens when pthreads collide, but what if we have each thread do something entirely different, no overlap?

We have found the maximum speedup parallel problems?

## Embarrassingly Parallel Problems 

The study of parallel algorithms has exploded over the past few years. An embarrassingly parallel problem is any problem that needs little effort to turn parallel. A lot of them have some synchronization concepts with them but not always. You already know a parallelizable algorithm, Merge Sort!

```C
void merge_sort(int *arr, size_t len){
     if(len > 1){
     //Mergesort the left half
     //Mergesort the right half
     //Merge the two halves
     }
```

With your new understanding of threads, all you need to do is create a thread for the left half, and one for the right half. Given that your CPU has multiple real cores, you will see a speedup in accordance with [Amdahl's Law](https://en.wikipedia.org/wiki/Amdahl's_law). The time complexity analysis gets interesting here as well. The parallel algorithm runs in O(log^3(n)) running time (because we fancy analysis assuming that we have a lot of cores.

In practice though, we typically do two changes. One, once the array gets small enough, we ditch the parallel mergesort algorithm and do a quicksort or other algorithm that works fast on small arrays (something something cache coherency). The other thing that we know is that CPUs don't have infinite cores. To get around that, we typically keep a worker pool.

## Worker Pool

We know that CPUs have a finite amount of cores. A lot of times we start up a number of threads and give them tasks as they idle.

## Few Drawbacks

You won't see the speedup right away because of things like cache coherency and scheduling extra threads.

## Other Problems

From [Wikipedia](https://en.wikipedia.org/wiki/Embarrassingly_parallel)
* Serving static files on a webserver to multiple users at once.
* The Mandelbrot set, Perlin noise and similar images, where each point is calculated independently.
* Rendering of computer graphics. In computer animation, each frame may be rendered independently (see parallel rendering).
* Brute-force searches in cryptography.[8] Notable real-world examples include distributed.net and proof-of-work systems used in cryptocurrency.
* BLAST searches in bioinformatics for multiple queries (but not for individual large queries) [9]
* Large scale facial recognition systems that compare thousands of arbitrary acquired faces (e.g., a security or surveillance video via closed-circuit television) with similarly large number of previously stored faces (e.g., a rogues gallery or similar watch list).[10]
* Computer simulations comparing many independent scenarios, such as climate models.
* Evolutionary computation metaheuristics such as genetic algorithms.
* Ensemble calculations of numerical weather prediction.
* Event simulation and reconstruction in particle physics.
* The marching squares algorithm
* Sieving step of the quadratic sieve and the number field sieve.
* Tree growth step of the random forest machine learning technique.
* Discrete Fourier Transform where each harmonic is independently calculated.
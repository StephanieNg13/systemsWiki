1. What do each of the Coffman conditions mean? (e.g. can you provide a definition of each one)
Hold and wait
Circular wait
Pre-emption
Mutual exclusion
2. Give a real life example of breaking each Coffman condition in turn. A situation to consider: Painters, paint and paint brushes.
Hold and wait
Circular wait
Pre-emption
Mutual exclusion
3. Be able to identify when Dining Philosophers code causes a deadlock (or not).
For example, if you saw the following code snippet which Coffman condition is not satisfied?
// Get both locks or none.
pthread_mutex_lock( a );
if( pthread_mutex_trylock( b ) ) { /*failed*/
   pthread_mutex_unlock( a );
   ...
}

4. How many of the following statements are true?
There can be multiple active readers
There can be multiple active writers
When there is an active writer the number of active readers must be zero
If there is an active reader the number of active writers must be zero
A writer must wait until the current active readers have finished
5. If one thread calls
  pthread_mutex_lock(m1) // success
  pthread_mutex_lock(m2) // blocks
and another threads calls
  pthread_mutex_lock(m2) // success
  pthread_mutex_lock(m1) // blocks
What happens and why? What happens if a third thread calls pthread_mutex_lock(m1) ?

6. Write code to implement a producer consumer using ONLY three counting semaphores. Assume there can be more than one thread calling enqueue and dequeue.
Determine the initial value of each semaphore.
7. Write code to implement a producer consumer using condition variables and a mutex. Assume there can be more than one thread calling enqueue and dequeue.
8. Use CVs to implement  add(unsigned int) and subtract(unsigned int) blocking functions that never allow the global value to be greater than 100.
9. Use CVs to implement a barrier for 15 threads.
10 How many processes are blocked? As usual assume that a process is able to complete if it is able to acquire all of the resources listed below.
P1 acquires R1
P2 acquires R2
P1 acquires R3
P2 waits for R3
P3 acquires R5
P1 waits for R4
P3 waits for R1
P4 waits for R5
P5 waits for R1
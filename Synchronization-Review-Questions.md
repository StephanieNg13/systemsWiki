# Topics
* Atomic operations
* Critical Section
* Producer Consumer Problem
* Using Condition Variables
* Using Counting Semaphore
* Implementing a barrier
* Implementing a ring buffer
* Using pthread_mutex
* Implementing producer consumer
* Analyzing multi-threaded coded

# Questions
* What is atomic operation?
* Why will the following not work in parallel code
```C
//In the global section
size_t a;
//In pthread function
for(int i = 0; i < 100000000; i++) a++;
```
And this will?
```C
//In the global section
atomic_size_t a;
//In pthread function
for(int i = 0; i < 100000000; i++) atomic_fetch_add(a, 1);
```
* What are some downsides to atomic operations? What would be faster: keeping a local variable or many atomic operations?
* What is the critical section?
* Once you have identified a critical section, what is one way of assuring that only one thread will be in the section at a time?
* Identify the critical section here

```C
struct linked_list;
struct node;
void add_linked_list(linked_list *ll, void* elem){
    node* packaged = new_node(elem);
    if(ll->head){
         ll->head = 
    }else{
         packaged->next = ll->head;
         ll->head = packaged;
         ll->size++;
    }
    
}

void* pop_elem(linked_list *ll, size_t index){
    if(index >= ll->size) return NULL;
    
    node *i, *prev;
    for(i = ll->head; i && index; i = i->next, index--){
        prev = i;
    }

    //i points to the element we need to pop, prev before
    if(prev->next) prev->next = prev->next->next;
    ll->size--;
    void* elem = i->elem;
    destroy_node(i);
    return elem;
}
```

How tight can you make the critical section?
* What is a producer consumer problem? How might the above be a producer consumer problem be used in the above section? How is a producer consumer problem related to a reader writer problem?
* What is a condition variable? Why is there an advantage to using one over a `while` loop?
* Why is this code dangerous?
```C
if(not_ready){
     pthread_cond_wait(&cv, &mtx);
}
```
* What is a counting semaphore? Give me an analogy to a cookie jar/pizza box/limited food item.
* What is a thread barrier?
* Use a counting semaphore to implement a barrier.

* Write up a Producer/Consumer queue, How about a producer consumer stack?
* Give me an implementation of a reader-writer lock with condition variables, make a struct with whatever you need, it just needs to be able to support the following functions
```C
void reader_lock(rw_lock_t* lck);
void writer_lock(rw_lock_t* lck);
void reader_unlock(rw_lock_t* lck);
void writer_unlock(rw_lock_t* lck);
```
The only specification is that in between `reader_lock` and `reader_unlock`, no writers can write. In between the writer locks, only one writer may be writing at a time.

* Todo: Analyzing mulithreaded code snippets
* Implementing a ring buffer
* Using pthread_mutex
* Implementing producer consumer
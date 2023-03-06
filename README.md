# CSN-232 Readers Writers Problem Starvation Free




The readers-writers problem is a classical problem of process synchronization, of multiple processes which are categorised into 2 types:
* Readers: They read the data from the shared location.
* Writers: They write the data into the shared location.

 - - - -
 
But the problem for starvation arises, but what exactly is starvation?

`Starvation` is when a process does not get the resources 	because the resource is being used by another processes for a significant time. This makes the process to wait forever and starve.
  - - - -
## Semaphores 
Semaphore is a structure that helps to control the entry of the processes into the critical section.

It has 2 functions wait() and signal() :-
* `wait()`- It decrement the value of semaphore and pushes the process to the rear of the queue.
* `signal()`- It increments the value of semaphore and if there exist any process in the queue it will pop it from the front and queues it into the ready queue.


  - - - -
## Data Structure Used:
`Queue (First-in-First-Out)`- This is used to queue all the processes that are blocked by the semaphore during the execution.


  - - - -
## Solution with Starvation

## Global Variables 

###	Semaphores:
* `writers_mutex`- Ensures that the wrtiers will get access to the shared memory.Initially the value is 1, i.e. `writers_mutex=1 (intially)`
*	`mutex`- Ensures the mutal exclusion of readers when they access the variable `count`. Initially the value is 1, i.e. `mutex=1 (intially)`


### Counter
 * `Int Count`- It counts the number of readers. It's initial value is 0, i.e. `count=0` intially.
 
## Implementation with Starvation:-

In this solution, There is a chance that writers will starve.

There are 2 mutex locks named `mutex` and `writers_mutex`.
`mutex` ensures the mutal exclusion of readers when they access the variable `count`. 

`writers_mutex` ensures that the writers will get access to the shared memory.

### Readers: 
```cpp
do{
    
    // Entry Section of Readers
    
    wait(mutex);
    count++;
    if(count == 1) wait(writers_mutex);
    signal(mutex);
    

    /*
      
      Critical Section
     
    */

   
   wait(mutex);
   count--;
   if(count == 0) signal(writers_mutex);
   signal(mutex);

   // Remainder Section

}while(true); 
```

## Writers:
```cpp
do{
   //Entry Section of Writers
  
   
   wait(writers_mutex);
   
   /* 

    Critical Section


   */ 
   signal(writers_mutex);
   

   // Remainder section

}while(true);
```
  - - - -
## Without Starvation 
## Global Variables 

We introduced a new semaphore `readers_mutex`, rest of the variables are same as in the first solution. 

###	Semaphores:
* `writers_mutex`- Ensures that the wrtiers will get access to the shared memory.
*	`mutex`- Ensures the mutal exclusion of readers when they access the variable `count`.
*	`readers_mutex`- Ensures to check that readers that come after the wrtiers are pushed into a FIFO queue of the semaphore `readers_mutex`.



### Counter
 * `Int Count`- It counts the number of readers. It's initial value is 0, i.e. `count=0` intially.
  
 
## Implementaion without Starvation:- 
### Readers:

```cpp
do{
    
    // Entry Section
    wait(readers_mutex);
    wait(mutex);
    count++;
    if(count == 1) wait(writers_mutex);
    signal(mutex);
    signal(readers_mutex);

    /**
     * 
     * Critical Section
     * 
    */

   // Exit Section
   wait(mutex);
   count--;
   if(count == 0) signal(writers_mutex);
   signal(mutex);

   // Remainder Section

}while(true);
```

If a readers is waiting for a writers process, the reader will go into the the FIFO queue of `readers_mutex`. This this mutex will ensure that all the processes will have the same priority level irrespective of their type being reader or writer.

### Writers:

```cpp
do{
   //Entry Section of Writers
  
   wait(readers_mutex);
   wait(writers_mutex);
  
   /* 

    Critical Section


   */ 
   signal(writers_mutex);
   signal(readers_mutex);

   // Remainder Section

}while(true);
```
Here `readers_mutex` will also ensure to add Writers into the FIFO queue which will ensure that both will run without any starvation.

All the processes will be scheduled in First Come First Serve manner. Thus this code will give the Solution to Readers & Writers Problem without any Starvation and Deadlocks.


  - - - -

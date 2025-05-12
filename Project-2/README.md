**Questions:**

1.  Notice that there is a maximum speed-up factor, but not necessarily using the most threads. Make a guess (i.e., write a short paragraph) as to why you think more threads aren't necessary better. Here's a hint: think about a group of people waiting to go through a turnstile (like at BART or Disney World). Are more people able to go through it just because there are more people?

In terms of the turnstile analogy, a bottleneck seems to exist that limits the number of threads 
that can "enter" the program and be utilized. In the case of hash-04, those bottlenecks seem to 
represent the sequential operations of the hashing algorithms. Only a certain percentage of hash-04
is parallel, so you will only be able to speed up that percentage of your program by using more 
threads. It seems we maxed out at a 10 times increase in Speedup which I believe indicates that 
about 10% of our program is serialized. Therefore the performance plateaus.
	
![test](images/Speedup%20vs%20Threads.png)

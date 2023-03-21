
# thread
thread = an independent serial execution -- registers, pc, stack

# memory share
in linux, threads can share memory, supports multiple threads sharing a user process's memory, as well as lots of threads in the kernel

# status

1. executing

the thread is using the CPU and registers and it has a stack and memory

2. not executing ("suspended", "blocked", "waiting")

registers must be saved somewhere in memory stack and memory need not be saved, just preserved
  
so when a thread is suspended, and then resumed must save state, and then restore it

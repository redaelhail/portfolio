---
title: "Threading in Python"
date: 2022-26-06T03:24:31Z
draft: false
---

Threads is one of the most important concepts in programming.


## What is a thread?

TODO

## Why use Threading?

Source code: http://www.dabeaz.com/usenix2009/concurrent/

##  Thread synchronization

### 1. Shared data and race problem

To respond to this question, let's launch two threads with no synchronization. The two threads called `foo` and `bar` are using the same variable `x`. One of them tries to increase the value with `1`  and the other subtract `1`

    # A simple example of a race condition

    import threading

    x = 0     # A shared value

    COUNT = 10000000
    def foo():
        global x
        for i in range(COUNT):
            x += 1

    def bar():
        global x
        for i in range(COUNT):
            x -= 1

    t1 = threading.Thread(target=foo)
    t2 = threading.Thread(target=bar)
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    print(x)


**Output**:

    PS \projects\threading> python race.py
    991536


After running this script, the two threads start running in "parallel". Whoever arrives first to use the variable `x` will be able to increase it or decrease it. Normally, the value of the variable `x` should be equal to `0` after the two threads finish working. Unfortunately this is not the case. In fact, we get random corrupt values at each run. So what's the problem here? It's obviously accessing shared data.

 Let's get back to the threads `foo` and `bar` and see what is happening at the interpreter level. Both functions have 4 stages of execution:

> 1. Load the global variable `x`
> 2. Load the `COUNT` variable
> 3. Addition (`foo`) or subtraction (`bar`)
> 4. Writing the result in the global variable `x`

**What is going on?**

When one of the threads starts execution and  loads both variables `x` and `COUNT`, it should normally start executing the steps 3 and 4, but the other thread may start execution at exactly that moment and loads the variables then makes changes on the global variable. The first thread resumes its execution without being aware of the changes on the global variable,  it will work with the loaded value at first instead of working with the updated version of the global variable.

When threads are not synchronized in accessing shared data, we are talking about a random race problem. To solve this problem there are several solutions:

1. `Locks`
2. `RLocks`
3. `Events`
4. `Semaphores`

### 2. Locks

It is a lock that can be acquired or released by any thread. When a thread A acquires the lock with `lock.acquire`, no other thread can access the variables until the thread A finishes its execution and runs `lock.release


    import threading

    x      = 0                   # A shared value
    x_lock = threading.Lock()    # A lock for synchronizing access to x

    COUNT = 1000000
    def foo():
        global x
        for i in range(COUNT):
            x_lock.acquire()
            x += 1
            x_lock.release()

    def bar():
        global x
        for i in range(COUNT):
            x_lock.acquire()
            x -= 1
            x_lock.release()

    t1 = threading.Thread(target=foo)
    t2 = threading.Thread(target=bar)
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    print (x)

**Output**:

    PS \projects\threading> python lock.py
    0

### 3. Rlocks

`Rlock` is the abbreviation of re-entering lock. Which means a Lock that can be acquired many times. So the thread who owns the lock is the last one who was calling Rlock. and not yet unlocking.

    import threading

    class Foo(object):
        lock = threading.RLock()
        def __init__(self):
            self.x = 0
        def add(self,n):
            with Foo.lock:
                print("adder")
                self.x += n
        def incr(self):
            with Foo.lock:
                print("subber")
                self.add(1)
        def decr(self):
            with Foo.lock:
                self.add(-1)

    # Two functions that will run in separate threads and call methods
    # on the above class.

    def adder(f,count):
        while count > 0:
            f.incr()
            count -= 1

    def subber(f,count):
        while count > 0:
            f.decr()
            count -= 1

    # Create some threads and make sure it works
    COUNT = 10
    f = Foo()
    t1 = threading.Thread(target=adder,args=(f,COUNT))
    t2 = threading.Thread(target=subber,args=(f,COUNT))
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    print (f.x)
    import threading

    class Foo(object):
        lock = threading.RLock()
        def __init__(self):
            self.x = 0
        def add(self,n):
            with Foo.lock:
                print("adder")
                self.x += n
        def incr(self):
            with Foo.lock:
                print("subber")
                self.add(1)
        def decr(self):
            with Foo.lock:
                self.add(-1)

    # Two functions that will run in separate threads and call methods
    # on the above class.

    def adder(f,count):
        while count > 0:
            f.incr()
            count -= 1

    def subber(f,count):
        while count > 0:
            f.decr()
            count -= 1

    # Create some threads and make sure it works
    COUNT = 10
    f = Foo()
    t1 = threading.Thread(target=adder,args=(f,COUNT))
    t2 = threading.Thread(target=subber,args=(f,COUNT))
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    print (f.x)

**Output**:

    PS \projects\threading> python rlock.py
    0
### 4. Events

    from threading import *
    import time

    def traffic_police():
    while True:
        time.sleep(5)
        print("Traffic Police Giving GREEN Signal")
        event.set()
        time.sleep(10)
        print("Traffic Police Giving RED Signal")
        event.clear()

    def driver():
    num=0
    while True:
        print("Drivers waiting for GREEN Signal")
        event.wait()
        print("Traffic Signal is GREEN...Vehicles can move")
        while event.isSet():
            num=num+1
            print("Vehicle No:", num," Crossing the Signal")
            time.sleep(2)
        print("Traffic Signal is RED...Drivers have to wait")

    event=Event()
    t1=Thread(target=traffic_police)
    t2=Thread(target=driver)
    t1.start()
    t2.start()

### 5. Semaphores

## Queues 

### Concept

### Inter Thread communication

### Example

    import threading
    import time
    from queue import Queue

    done = threading.Semaphore(0)
    q = Queue()

    def producer(queue):
        
        print ("I'm the producer and I produce data.")
        print ("Producer is going to sleep.")
        time.sleep(10)
        item = "Hello"
        q.put(item)
        print ("Producer is alive. Signaling the consumer.")
        done.release()

    def consumer(queue):
        print ("I'm a consumer and I wait for data.")
        print ("Consumer is waiting.")
        done.acquire()
        print ("Consumer got", queue.get())

    t1 = threading.Thread(target=producer, args=[q])
    t2 = threading.Thread(target=consumer, args=[q])
    t1.start()
    t2.start()

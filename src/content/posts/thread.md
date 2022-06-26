---
title: "Threading synchronization"
date: 2022-26-06T03:24:31Z
draft: false
---


## What is a thread?

TODO

## Why use Threading?

Source code: http://www.dabeaz.com/usenix2009/concurrent/


###  Thread synchronization

**Shared data and race problem**

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


## Locks


## Rlocks
`Rlock` is the abbreviation of re-entering lock. Its
Its a lock that can be acquired or released by any thread. When a thread A acquires the lock with `lock.acquire`, no other thread can access the variables until the thread A finishes its execution and runs `lock.release

## Events

## Semaphores
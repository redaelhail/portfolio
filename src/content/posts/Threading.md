---
title: "docker"
date: 2022-06-06T13:24:31Z
draft: false
---


# What is a thread?

# Why use Threading?

Source code: http://www.dabeaz.com/usenix2009/concurrent/


##  Thread synchronization

#### What is threrad synchronization and why we need it?

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

Output:

    PS \projects\threading> python race.py
    991536
    PS \projects\threading> python race.py
    -3600072

## Rlock

## 
---
title: Go - Channels VS Mutexes
---

I'm actually learning GO for a project and I have previously made a note on `Channels`([[go_channels]]) in `GO`, but I recently learned about `Mutexes`, just like in C.
So i decided to learn when to use Channels and when to use Mutexes.

# Channels VS Mutexes

In the [Go Wiki](https://go.dev/wiki/MutexOrChannel) they adviced to `use whichever is most expressive and/or simple`.
They also give a general guide:
- Channel:
    - Passing ownership of data
    - Distributing units of work
    - Communicating async results
- Mutex:
    - Caches
    - States

There is not so much to say for now.


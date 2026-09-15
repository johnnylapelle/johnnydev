---
title: Context
---

# What is a Context ?

A `Context` is an object that get passed along the call stack from function to function.
It's like an emergency button, that tell background tasks to stop working because de user cancels the action or the request timed out, etc.

It's two main jobs are:
- Cancellation
- Value passing: carrying request-specific data

## Why do we need those ?
Imagine if a user request something from a server and cancel it, instead of running the request for nothing, the context will indicate to stop the work immediatly, freeing up server resources.

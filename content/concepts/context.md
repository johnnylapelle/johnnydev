---
title: Context
draft: true
---

# What is Context
A context is a way to stops a goroutine executing a function if something went wrong.

For example if the user cancel their request, by closing the browser, we want to stop the the request.

In a project where I coded a client and a server, when we kill the server with `ctrl+C` the context get that signal and can close the connections, frees up memories, etc.

``` 
func doSomething(ctx context.Context) error {
    for {
        select {
        case <-ctx.Done():
            return ctx.Err()
        default:
            // do the default thing
        }
    }
}
```

# Rules of thumb
- `ctx` is always the first parameter in the function's argument.
- Never pass `nil`. Use `context.TODO` if you're not sure what context to use.
- Always call the `cancel` function returned by the context constructors.

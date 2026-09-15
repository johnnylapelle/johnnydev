# Go: Channels

## What i understand:
A Channel is a way to avoid data race when calling multiple goroutine.

Init a Channel is made with  `make(chan <type>)`. The type argument is the type of the variable passed (int, string, float64, etc).

A Channel is like a FIFO queue, when a goroutine sends data through it, the others are blocked.


```
package main

import "fmt"

func main() {

    messages := make(chan string, 2)

    messages <- "buffered"
    messages <- "channel"

    fmt.Println(<-messages)
    fmt.Println(<-messages)
}
```

```	
$ go run channel-buffering.go 
buffered
channel
```

*Snippet is from [Go by example](https://gobyexample.com/channel-buffering)*


In this code, we use `anonymous function` to launch go routine, that will send the struct result through the Channel. When the scope of the anonymous function is done, the goroutines are terminated.
Then the main process get all the `result struct` in the second loop and assign one at a time the results, avoinding data race.

```
package concurrency

type WebsiteChecker func(string) bool
type result struct {
	string
	bool
}

func CheckWebsites(wc WebsiteChecker, urls []string) map[string]bool {
	results := make(map[string]bool)
	resultChannel := make(chan result)

	for _, url := range urls {
		go func() {
			resultChannel <- result{url, wc(url)}
		}()
	}

	for i := 0; i < len(urls); i++ {
		r := <-resultChannel
		results[r.string] = r.bool
	}

	return results
}
```

*Snippet is from [Learn Go with Tests](https://quii.gitbook.io/learn-go-with-tests/go-fundamentals/concurrency#channels)*

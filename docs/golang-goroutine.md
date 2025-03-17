## Goroutine
- Normally, Go will run in order (Step by Step), but with Goroutine, functios will run concurrently
```go
package main

import (
	"fmt"
	"time"
)

func printMessage() {
	fmt.Println("Hello from Goroutine!")
}

func main() {
	go printMessage() // Runs in a separate Goroutine
	fmt.Println("Main function") // Runs immediately

	time.Sleep(time.Second) // Wait to see Goroutine output
}
```
- To make sure Goroutine function done before programe exit, we will use *sync.Waitgroup*
```golang
package main

import (
	"fmt"
	"sync"
)

func printMessage(wg *sync.WaitGroup) {
	defer wg.Done() // Mark this Goroutine as done
	fmt.Println("Hello from Goroutine!")
}

func main() {
	var wg sync.WaitGroup // Create a WaitGroup
	wg.Add(1)             // Tell WaitGroup to wait for 1 Goroutine

	go printMessage(&wg) // Start Goroutine

	wg.Wait() // Wait until all Goroutines in WaitGroup are done
}
```
### sync.WaitGroup
- This struct belong to `sync` package
- It acts like a counter:
- - Add(n): Increase the counter when starting Goroutine
- - Done(): Decreate the counter when a Goroutine finishes
- - Wait(): Block execution until the counter reaches zero 

## Channels in Go
- Channel is a way help Goroutine can communicate safely. Allows sending and receiving data between Goroutine
```go
ch := make(chan string)
```
Example
```go
packagge main
import "fmt"
func main() {
    ch := make(chan string) //Create a channel

    go func(){
        ch <- "Hello form Goroutine" //Send data to channel
    }()

    message := <-ch // Receive data from channel
    fmt.Print(message)
}
```
### Buffered and Unbuffed on Channel
- If you declare a channel without specify the capacity it can store, default buffer = 1 = no buffer. Channel only receive data and send 1 value and block other command input
```go
ch := make(chan string) //Un-buffered
ch := make(chan string, 2) //Buffered = 2
```

### Close chanell
`close(ch)`
### Muliple Channels (Select Statement)
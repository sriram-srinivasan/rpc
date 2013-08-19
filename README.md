# net/rpc + server-side async

This is a drop-in replacement for golang's net/rpc that allows the server side to be asynchronous as well. Hopefully this will be absorbed in the standard library.

Go1.1's standard net/rpc package allows the client to make asynchronous calls, but the corresponding service method on the server side is expected to synchronously return a value. If this method has to wait for another asynchronous process to finish, the goroutine is unnecessarily blocked. 

### Example

####Synchronous 
(the standard example from [net/rpc](http://golang.org/pkg/net/rpc/))

```
func (t *Arith) Multiply(args *Args, reply *int) error {
	*reply = args.A * args.B
	return nil
}
```

###Asynchronous

```
import "github.com/sriram-srinivasan/rpc"

func (t *Arith) Multiply(args *Args, _ *int) error {
        fut := rpc.NewFuture()
        callLater(fut, args)
        return fut // no message sent back to caller
}

func callLater(fut Future, args *Args) {
      go func() {
          time.Sleep(time.Second) // artificial delay
          fut.Set(args.A * args.B, /* err = */ nil) // message sent here.
      }()
}
```

## Install

```
go get github.com/sriram-srinivasan/rpc
go test github.com/sriram-srinivasan/rpc
```

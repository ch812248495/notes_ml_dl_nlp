**Some concepts of server end**

Request: cliencts' request information, used for analuzing `post`,`get`,`cookie`,`url`

Response: the information server have to feed back

Conn: users' request connection

Handler: handle logic when dispose request and generate return information.

**The mechanism to analyze the http packets**

![](/assets/eeee.png)

* establish the Listen Socket, monitor the assigned port waiting for teh clients' request
* Listen Socket accept the request from the client end, get the Client Socket and communicate by it.
* dispose the request from the client end, read the HTTP protocol head first. If it is POST, read the submitted data and head in to the corresponding handler.The handler dispose the data and prepare the data which the client needs, pass it to the client through Client Socket.

Those all things are ahieved by a function `ListenAndServe`, here is the source code:

```go
func (srv *Server) Serve(l net.Listener) error {
    defer l.Close()
    var tempDelay time.Duration // how long to sleep on accept failure
    for {
        rw, e := l.Accept()
        if e != nil {
            if ne, ok := e.(net.Error); ok && ne.Temporary() {
                if tempDelay == 0 {
                    tempDelay = 5 * time.Millisecond
                } else {
                    tempDelay *= 2
                }
                if max := 1 * time.Second; tempDelay > max {
                    tempDelay = max
                }
                log.Printf("http: Accept error: %v; retrying in %v", e, tempDelay)
                time.Sleep(tempDelay)
                continue
            }
            return e
        }
        tempDelay = 0
        c, err := srv.newConn(rw)
        if err != nil {
            continue
        }
        go c.serve()
    }
}
```

Those code are for monitoring.

![](/assets/f24f.png)


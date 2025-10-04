---
Tech Stack: Go
Youtube Channel Name: Anthony GG
Hours: 09:57:00
Status:
---
```Makefile
build:
  @go build -o bin/fs
run: build
  @./bin/fs
test:
  @go test ./... -v
```

A ==Makefile== is like a script for automating tasks. Instead of remembering and typing long commands every time, you define them once, and then just run `make <task>`.

> [!Packages]
> - p2p

---
# Definitions:

> [!Node] 
>One running instance of your program (your go service)
>- If 5 people run your program, that’s 5 nodes in the network.
>- Each node can *store data*, *communicate with other nodes*, and *participate in replication/coordination*.
>So, it is a local process (your program instance). It has ==storage==, ==metadata==, and a ==networking layer==.

> [!Peer]
> Represents a remote node from the perspective of this node.
> Example: If Node A knows about Node B and Node C, then in Node A's code, B and C are peers.

>[!Transport]
>Defines how nodes connect and talk to peers (TCP, UDP, Websockets, etc...)

**Why separate them**?

Node: The whole participant in the system.
Peer: Abstraction for "a remote node I'm talking to".
Transport: The phone or messaging app you use to talk.

---
#### P2P:

The package is peer-to-peer. This will contain all the logic for networking between nodes in your distributed file storage system.

```go
package p2p

// Peer is an interface that represents the remote node
type Peer interface{}

// Transport is anything that handles the communication
// between nodes in a network
type Transport interface{}
```


```go
package p2p

import "net"

type TCPTransport struct {
	listenAddress string
	listener      net.Listener
	peers         map[net.Addr]Peer
}
```


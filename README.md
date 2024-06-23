# Go gRPC
Example of working with gRPC for Go based on a [repository](https://github.com/grpc/grpc-go).

## Usage
- download repository: `git clone https://github.com/gocias/go-grpc`
- go to the project: `cd go-grpc/`
- first terminal (server): `go run server/main.go`
- second terminal (client): `go run client/main.go`

## General algorithm
- new functionality (change file) `proto/helloworld.proto`
- do recompiling
- add functionality to the server `server/main.go`
- add functionality processing to the client `client/main.go`

## Explanations
In the proto file `proto/helloworld.proto` we create a new method:
```protobuf
// Sends something
rpc SaySomething (HelloRequest) returns (HelloReply) {}
```

In the server file `server/main.go` we implement the logic of the new method:
```go
func (s *server) SaySomething(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
    return &pb.HelloReply{Message: "Say something here: " + in.GetName()}, nil
}
```

In the client file `client/main.go` we process the logic of the new method:
```go
r, err = c.SaySomething(ctx, &pb.HelloRequest{Name: *name})
if err != nil {
    log.Fatalf("could not greet: %v", err)
}
log.Printf("Something: %s", r.GetMessage())
```

Where `c` is the connection `pb.NewGreeterClient(conn)`, 
and `pb` is a link to the proto file at `go-grpc/proto` for the module `gocias.ru/go-grpc`.
Import: `pb "gocias.ru/go-grpc/proto"`

## Hints
Stop the server on port `50052`.
```shell
sudo kill -9 `sudo lsof -t -i:50052`
```

Recompiling the updated `.proto` file.
```shell
protoc --go_out=. --go_opt=paths=source_relative \
    --go-grpc_out=. --go-grpc_opt=paths=source_relative \
    proto/helloworld.proto
```

## Peculiarities
- use `grpc.NewClient` instead of `grpc.Dial`

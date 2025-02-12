# grpc-json-proxy

GRPC JSON is a proxy which allows HTTP API tools like Postman to interact with gRPC servers.

## Requirements
- grpc+json codec must be enabled on the grpc server
- Postman must be configured to use the proxy

Configuration of the proxy and its dependencies is a three step process.

1. Register a JSON codec with the gRPC server. In Go, it can be automatically registered simple by adding the following import:

```go
import "github.com/daneshvar/grpc-json-proxy/codec"

func init() {
    codec.Register()
}
```

If you're using `gogo/protobuf` as your protobuf backend, import the following:

```go
import "github.com/daneshvar/grpc-json-proxy/gogoprotobuf/codec"

func init() {
    codec.Register()
}
```

2. Run the grpc-json-proxy. Download pre-built binaries from https://github.com/daneshvar/grpc-json-proxy/releases/ or build from source:

`go get -u github.com/daneshvar/grpc-json-proxy`

`grpc-json-proxy`

Other way, you can simply use `grpc-json-proxy` docker image out of the box:

```bash
docker run -p 7001:7001 jnewmano/grpc-json-proxy
```

3. Configure Postman to send requests through the proxy.
Postman -> Preferences -> Proxy -> Global Proxy

`Proxy Server: localhost 7001`


![Postman Proxy Configuration](https://cdn-images-1.medium.com/max/1600/1*oc09cwpCC9XrjpU9Gl5YTw.png)

Setup your Postman gRPC request with the following:

1. Set request method to Post .
1. Set the URL to http://{{ grpc server address}}/{{proto package}}.{{proto service}}/{{method}} Always use http, proxy will establish a secure connection to the gRPC server.
1. Set the Content-Type header to application/grpc+json .
1. Optionally add a Grpc-Insecure header set to true for an insecure connection.
1. Set the request body to appropriate JSON for the message. For reference, generated Go code includes JSON tags on the generated message structs.


For example:

![Postman Example Request](https://cdn-images-1.medium.com/max/1600/1*npRlBiKxuJ5KMnnk0F5n6g.png)



Inspired by Johan Brandhorst's [grpc-json](https://jbrandhorst.com/post/grpc-json/)

### Host accessibility

If you use docker image to run grpc-json-proxy server, and want to access grpc via loopback address `127.0.0.1`, you should pay attention to docker network accessibility.

1. use `host.docker.internal` instead of `127.0.0.1` in Linux.
2. use `docker.for.mac.host.internal` instead of `127.0.0.1` in MacOS and with Docker for Mac 17.12 or above.

See: https://docs.docker.com/docker-for-mac/networking/#use-cases-and-workarounds

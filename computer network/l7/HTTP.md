Different [networking layers](Networking%20as%20OSI%20Model) operate with the layer above or below it. In the case of HTTP, it relies on the layer four [TCP/IP](TCP.md) for transportation of data by managing the following aspects
1. connection establishment
2. reliable data transmission, which is not provided by IP
3. stream-based communication
4. (IP) addressing and packet routing 
5. (IP) large data is split into smaller IP packets, reassembled at destination

The [transport layer](OSI%20Transport%20Layer.md) manages the transportation of data, where as HTTP, as a [application layer](OSI%20Application%20Layer.md) protocol, provides an abstraction for application interaction in an effective manner. It also serves as a foundation for more advanced protocols like [[gRPC]] and [[WebSockets]].

The main features of HTTP are
1. stateless
2. represents request intentions with distinct methods
3. represents response status with status codes
4. provides metadata of request in the form of headers
5. provides cookies as mechanism for some state persistence (even though it's stateless by default)
6. provides customisable level of caching

## Protocol Conventions
### Representation of meaning
As a application layer protocol, HTTP focuses on meaningful representation of requests and responses - unlike the lower [[OSI Transport Layer]] which focuses on transporting the data.

The HTTP protocol defines a set of methods, each indicating a particular intention, the methods are
- **GET**: Retrieve a resource.
- **POST**: Submit data to a server (e.g., form submission).
- **PUT**: Update or create a resource.
- **DELETE**: Delete a resource.
- **HEAD**: Retrieve headers only (no body).
- **OPTIONS**: Discover supported HTTP methods for a resource.
- **PATCH**: Partially update a resource.

and to indicate the various statuses of a request, the server uses the following set of status code
- **1xx (Informational)**: Processing is ongoing (e.g., 101 Switching Protocols).
- **2xx (Success)**: Request was successful (e.g., 200 OK, 201 Created).
- **3xx (Redirection)**: Further action required (e.g., 301 Moved Permanently, 304 Not Modified).
- **4xx (Client Errors)**: Issues with the request (e.g., 400 Bad Request, 404 Not Found).
- **5xx (Server Errors)**: Issues on the server side (e.g., 500 Internal Server Error, 503 Service Unavailable)

Request and responses may also require metadata - data which describes the request and response, these metadata take the form of HTTP headers.
### Stateless
The HTTP operates in a *stateless* manner, where the server does not retain the information of the previous request. Each new request is treated as a new interaction, with no inherent memory of the previous request.

Statelessness is great for scaling and simplicity, where the server does not need to spend resources in maintaining session information between requests, and multiple requests can be processed independently. This also enables horizontal scaling and load balancing.

### State with Cookies?
Although the HTTP is stateless by design, it offers some level of state persistence in the form of client cookies - cookies are small client-side data which indicates the state of the current session. They are often linked with sessions.

For example, server can respond with a authenticate code which client can store as a cookie and reuse in all future requests, and all the future requests will be authenticated without the client having to re-authenticate.

### Connections
In networking, connection is both a logical and physical construct. HTTP relies on [[TCP]] to establish and maintain the physical connection over the network.

In the older `HTTP/1.0` version, each request opens a new connection. In later versions, the same connection is used - this differentiation is known as non-persistent and persistent connection.

## [HTTP Caching](HTTP%20Caching.md)
See: [Mozilla Reference](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)

HTTP caching stores the response associated with a request, and reuses the same response. Thereby reducing response time for the client and reducing the load on the server.

As the networking setup may involve multiple [proxies](Network%20Proxies), HTTP server also needs to be aware of how the caching behaviour differs across the client, forward proxy, and reverse proxy.
## HTTP Security - [[HTTPs]]


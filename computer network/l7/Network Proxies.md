A network proxy is an intermediary server which sits between a client and a destination (origin) server. It intercepts and forwards the requests from the client to the server, and relays the server's response back to the client.

Proxies are used for various purposes, including
1. improving performance
2. enhancing security

## Forward Proxy
A forward proxy sits between a client and the server, which forwards the client's request to the server. It can sometimes be used to hide the client's IP, filter or block access to certain websites or content.

## Reverse Proxy
A reverse proxy forwards the client request to one or more backend servers. It is able to load balance by distributing requests across multiple backend servers. It can also perform access control, caching, and offers [SSL](SSL,%20TLS) termination.


![[network_proxies.png]]
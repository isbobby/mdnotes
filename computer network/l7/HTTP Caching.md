See: [Mozilla Reference](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)

Caching is an important aspect of any network system, it reduces response time by reusing the same response, and reduces the load on server.

# Types of Caches in HTTP
In the context of HTTP, there are two types of caches - private cache and shared cache.
## Private Cache
A private cache is a cache tied to a specific client, typically a browser cache. Since the cache is not shared with other clients, its content remains private.

A private cache can store personalised information for that user.

However, if personalised contents are stored in a cache other than private cache, then the other users may be able to retrieve those contents, which may cause unintentional information leakage.

If a response contains personalised content and you want to store the response only in the private cache, you must specify a `private` directive, where

```
Cache-Control: private
```

## Shared Cache
The share cache no longer reside on the client, but is instead located between the client and the server, these shared caches are further sub-classified into proxy caches and managed caches.

### [Proxy](Network%20Proxies.md) Cache
In addition to access control, some proxies also implement caching to reduce the load on the network. Since the cache exists on the proxy and not directly managed by the developer, the cache control relies on some appropriate HTTP headers.

In recent years, as [[HTTPs]] becomes more widely adopted, some proxies may only be able to tunnel the encrypted response and can't behave as a proxy cache.

### Managed caches
Managed caches are explicitly deployed by service developers to offload the origin server, and to deliver content effectively. Examples include [reverse proxies](Network%20Proxies), [[CDN]]s, and service workers in combination with the cache API.

The characteristics of managed caches vary depending on the type of product deployed.


# Networking 101 

The internet is a global network of computers that communicate using IP addresses. 

When access something online:

```
URL -> DNS -> IP -> TCP/TLS -> HTTP -> Endpoint -> Response
```

You type: https://google.com

1. DNS transform domain name to IP 
2. Routing. Packets travel via routers
3. TCP connection to establish reliable connection (transport layer to deliver the message). It ensures data arrives, in the correct order without loss. 
4. TLS handshake (security layer to encrypt the data)
5. HTTP request 
6. CDN (optional) serve cached content if possible 
7. Server processes (i.e. runs code, queries database)
8. Response 
9. Browser renders 


URL = protocol + domain + path 
example: `https://api.example.com/users/123`
- this is a URL 
- the `/users/123` can be designed RESTfully
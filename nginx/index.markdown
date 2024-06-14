---
layout: page
permalink: /nginx/
title: Nginx
---

Page dedicated to knowledge related to NGINX.

### Name-based server

Nginx first decides which server should process the request

```
server {
    listen      80;
    server_name example.org www.example.org;
    ...
}

server {
    listen      80;
    server_name example.net www.example.net;
    ...
}

server {
    listen      80;
    server_name example.com www.example.com;
    ...
}
```

Nginx tests the request's header field "Host" to determine which server the request should be routed to. If it does not match any server name or the header is missing, nginx will route to default. Default can be set explicitly:

```
server {
    listen      80 default_server;
    server_name example.net www.example.net;
    ...
}
```

### IP-based virtual servers

Nginx first tests the IP address and port of the reuqest, then the "Host" header.

```
server {
    listen      192.168.1.2:80;
    server_name example.com www.example.com;
    ...
}
```

Different default servers may be defined for different ports:

```
server {
    listen      192.168.1.1:80 default_server;
    server_name example.net www.example.net;
    ...
}

server {
    listen      192.168.1.2:80 default_server;
    server_name example.com www.example.com;
    ...
}
```

### Location to process a request

First, nginx searches for the most specific prefix location (such as "/") to use as a last resort. Then, nginx checks locations given by regular expression in the order listed in the configuration. The first matching expression is chosen.

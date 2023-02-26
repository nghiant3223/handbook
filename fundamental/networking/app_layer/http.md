# HTTP

## Header

### Keep Alive

HTTP keep-alive, a.k.a., HTTP persistent connection, is an instruction that allows a single TCP connection to remain open for multiple HTTP requests/responses.

By default, HTTP connections close after each request. When someone visits your site, their browser needs to create new connections to request each of the files that make up your web pages (e.g. images, Javascript, and CSS stylesheets), a process that can lead to high page load times.

Enabling the keep-alive header allows you to serve all web page resources over a single connection. Keep-alive also reduces both CPU and memory usage on your server.

## References

- [HTTP Keep-Alive](https://www.imperva.com/learn/performance/http-keep-alive/)

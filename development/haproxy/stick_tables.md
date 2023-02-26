# Stick Tables

## Syntax

To provision a stick table, add a stick-table line to a frontend, backend, or listen section. Typically, you place it in a frontend or listen section if you plan to use the stick table only in that same section and place it in a backend if you plan to reference it in other sections.

In the following example, we define a stick table that tracks the HTTP request rate of each client that passes through the load balancer:

```
frontend www
   bind :80
   stick-table  type ip  size 1m  expire 10s  store http_req_rate(10s)
```

- The table's primary key is of type ip, which means that the keys will be IP addresses
- The table holds a maximum of 1m records, which is 1048576 records
- A record expires after 10 seconds unless it is accessed during that time
- We store (associate) one counter with each IP address, http_req_rate(10s), which calculates the HTTP request rate over the last 10 seconds

You can track more than one counter, as shown below:

```
frontend www
   bind :80
   stick-table  type ip  size 1m  expire 10s  store http_req_rate(10s),conn_rate(10s),bytes_in_rate(10s)
```

Invoke http-request track-sc0 to add a record to the table. It takes a fetch method whose value will be set as the key in the table. In the following example, we use the client's source IP address, which we get with the src fetch method, as the key. We also include the http-request deny directive to deny any client whose request rate goes above 10:

```
frontend www
   bind :80
   stick-table  type ip  size 1m  expire 10s  store http_req_rate(10s)
   http-request track-sc0 src
   http-request deny if { sc_http_req_rate(0) gt 10 }
```

When the IP address goes into the table, the associated HTTP request rate counter begins counting that client's rate of requests. Each time that the same client makes a request, this record and its associated counter updates.

The http-request deny directive rejects clients with an HTTP request rate greater than 10 within the time period tracked by the counter: 10 seconds.

## Data types for keys

Choose any of the following data types depending on the keys you will store:

- ip
- ipv6
- integer
- string len <length>
- binary len <length>

For example, you could track the HTTP request rate on a per-backend basis rather than on a per-client basis by setting the table's type to string. Then, change the http-request track-sc0 directive to capture the name of the backend using the be_name fetch method:

```
frontend www
   bind :80
   stick-table  type string  size 1m  expire 10s  store http_req_rate(10s)
   default_backend webservers
   use_backend apiservers if { path /api }

backend webservers
  server s1 192.168.50.20:80
  http-request track-sc0 be_name table www

backend apiservers
  server s1 192.168.50.21:80
  http-request track-sc0 be_name table www
```

## Stick table sizes

Choose any of the supported size suffixes when defining your stick table:

k: 2^10 = 1024
m: 2^20 = 1048576
g: 2^30 = 1073741824

## Reference a stick table

To reference a stick table in multiple parts of your configuration, move it into its own backend section. The name of the backend becomes the name of the stick table, as shown below:

```
backend mysticktable
   stick-table  type ip  size 1m  expire 10s  store http_req_rate(10s)

frontend www
   bind :80
   http-request track-sc0 src table mysticktable
   http-request deny if { sc_http_req_rate(0,mysticktable) gt 10 }
```

## Sticky counters
A sticky counter is a variable that temporarily holds the key value to look up in the stick table.

For example, if you want to store a client's HTTP request rate, you first get their source IP address using the src fetch method. You then store that value in sticky counter 0 by using track-sc0:

```
frontend www
   bind :80
   http-request track-sc0 src table mysticktable
```

An http-request track-sc0 line inserts or updates a record in a stick table. The sc0 part specifies the sticky counter to use. By default, there are three variants of this function, depending on which sticky counter you want to use:

- http-request track-sc0
- http-request track-sc1
- http-request track-sc2

The sticky counter variable sc0 holds the IP address as you create or update the stick table entry. You can track multiple aspects of a request by invoking the other track-sc directives:

```
frontend www
   bind :80

   # key is source IP address
   http-request track-sc0 src  table sticktable1

   # key is backend name
   http-request track-sc1 be_name table sticktable2

   # key is Host header
   http-request track-sc2 req.hdr(Host) table sticktable3
```

All fetch methods that retrieve a record from a stick table use the ID of the sticky counter that holds the key. For instance, the sc_http_req_rate fetch takes the sticky counter number as its first parameter:

```
http-request deny if { sc_http_req_rate(0,mysticktable) gt 10 }
```

## General-purpose counters
There are a number of built-in counters that track a predefined aspect of a request, such as http_req_rate(<period>), which tracks HTTP request rate.

There are also general-purpose counters, which you can increment manually.

The following stick table registers two general-purpose counters: gpc0 and gpc1:

```
frontend www
   bind :80
   stick-table  type ip  size 1m  expire 10s  store gpc0,gpc1
   http-request track-sc0 src
   http-request sc-inc-gpc0(0) if { req.hdr(Host) example.com }
   http-request sc-inc-gpc1(0) if { url_param(example) test }
```

Use http-request sc-inc-gpc0(0) to increment gpc0 and http-request sc-inc-gpc1(0) to increment gpc1. In the previous example, gpc0 increments whenever a request's Host header equals example.com. The gpc1 counter increments whenever the request includes a URL parameter named example that's set to test.

Use the gpc0_rate(<period>) and gpc1_rate(<period>) counters to track the rate at which the gpc0 and gpc1 counters increment during the given time period:

```
frontend www
   bind :80
   stick-table  type ip  size 1m  expire 10s  store gpc0,gpc1,gpc0_rate(10s),gpc1_rate(10s)
```

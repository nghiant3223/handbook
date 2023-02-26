![](https://cdn.haproxy.com/wp-content/uploads/2021/08/HAProxy_Basics_-_Health_Checks.png)

_HAProxy provides active, passive, and agent health checks._

HAProxy makes your web applications highly available by spreading requests across a pool of backend servers. If one or even several servers fail, clients can still use your app as long as there are other servers still running.

The caveat is, HAProxy needs to know which servers are healthy. That’s why _health checks_ are crucial. Health checks automatically detect when a server becomes unresponsive or begins to return errors; HAProxy can then temporarily remove that server from the pool until it begins to act normally again. Without health checks, HAProxy has no way of knowing when a server has become dysfunctional.

Note: Health checks complement other fail-safe measures in HAProxy such as retries and redispatches. Read our blog post [HAProxy Layer 7 Retries and Chaos Engineering](https://www.haproxy.com/blog/haproxy-layer-7-retries-and-chaos-engineering/) to learn more.

You have access to three types of health checks: _active_, _passive_, and _agent_. Let’s learn about each one.

## [](https://www.haproxy.com/blog/how-to-enable-health-checks-in-haproxy/#active-health-checks)Active Health Checks

The simplest solution is to poll your backend servers by attempting to connect at a defined interval. This is known as an _active health check_. If HAProxy doesn’t get a response back, it determines that the server is unhealthy and after a certain number of failed connections, it removes the server from the rotation.

If you want to keep the default settings, configuring an active health check involves simply adding a `check` parameter to a `server` line in a backend. In the following example, we’ve enabled active health checks for each server:

HAProxy will try to establish a TCP connection every two seconds. After three failed connections, the server is removed, temporarily, until HAProxy gets at least two successful connections, after which it reinstates the server into the backend. You can customize these settings, changing the interval, number of failed checks that trigger a removal, or the number of successful checks that reinstate the server.

The `inter` parameter changes the interval between checks; it defaults to two seconds. The `fall` parameter sets how many failed checks are allowed; it defaults to three. The `rise` parameter sets how many passing checks there must be before returning a previously failed server to the rotation; it defaults to two. In the example below, we’ve set new values:

While attempting to connect helps determine whether an application is up and running, it can’t tell you whether the app is behaving normally. For web applications, you can switch to using an HTTP health check instead. An HTTP health check sends an HTTP request and expects a successful response in the 2xx or 3xx range, such as _200 OK_ or _302 Found_.

Just add `option httpchk` to the backend, as shown:

By default, HAProxy makes a GET request to the URL path **/**, but you can change that by adding an `http-check send` line. Below, we send a GET request to the URL path **/health**. A common technique is to program the **/health** endpoint to do a thorough check of your application and its dependencies and then return a single successful response if everything looks good.

```
backend webservers
  option httpchk
  server server1 192.168.50.2:80 check
  server server2 192.168.50.3:80 check
  server server3 192.168.50.4:80 check
```

To send a POST request with a JSON body, use this form, which includes a _Content-Type_ request header and a message body:

```
backend webservers
  option httpchk
  http-check send meth GET  uri /health
  server server1 192.168.50.2:80 check
  server server2 192.168.50.3:80 check
  server server3 192.168.50.4:80 check
```

While it is a common pattern to have the server do a thorough check on its end, you can also configure HAProxy to perform several checks too. In the example below, we define two checks, both of which must be successful. Each block starts with `http-check connect`.

```
backend webservers
  option httpchk

  http-check connect
  http-check send meth GET uri /health
  http-check expect status 200

  http-check connect
  http-check send meth GET uri /health2
  http-check expect status 200

  server server1 192.168.50.2:80 check
  server server2 192.168.50.3:80 check
  server server3 192.168.50.4:80 check
```

The `http-check connect` directive also lets you connect to the server using SSL and specify the protocol, such as HTTP/2, by using ALPN, as shown below:

```
http-check connect ssl  alpn h2,http/1.1
```

Something else that you can do is tell HAProxy to expect a certain status code to be returned or that a string should be included in the HTTP response body. Use the `http-check expect` directive with either the _status_ or _string_ keyword. In the following example, the application must return a _200 OK_ response status to be considered healthy:

```
backend webservers
  option httpchk
  http-check send meth GET uri /health
  http-check expect status 200
  server server1 192.168.50.2:80 check
  server server2 192.168.50.3:80 check
  server server3 192.168.50.4:80 check
```

Or, you can require the response body to contain a case-sensitive string, such as _success_:

```
http-check expect string success
```

HAProxy also supports other protocol-specific health checks for LDAP, MySQL, PostgreSQL, Redis, and SMTP.

## [](https://www.haproxy.com/blog/how-to-enable-health-checks-in-haproxy/#passive-health-checks)Passive Health Checks

Whereas an active health check continually polls the server with either a TCP connection or an HTTP request, a _passive health check_ monitors live traffic for errors. You can enable this mode by adding the `check`, `observe`, `error-limit`, and `on-error` parameters to a server line, as shown below:

```
backend webservers
  option httpchk
  http-check send meth GET uri /health
  server server1 192.168.50.2:80 check  observe layer7  error-limit 50  on-error mark-down
```

Set the `observe` parameter to _layer4_ to monitor all TCP connections for problems or to _layer7_ to watch all HTTP responses for errors. Successful responses are those that have an HTTP status code in the range 100-499, 501 or 505. The `error-limit` parameter sets how many consecutive requests can have errors before the `on-error` rule kicks in. Here, the rule marks the server as down.

Passive health checks always coexist with active health checks, with the latter doing its normal polling while also being responsible for reviving a server after it has been marked as down by a passive health check. In other words, you get both types of checking simultaneously. The benefit of that is that you will detect when only a part of your web application is malfunctioning, even if the active health check URL isn’t targeting that part. For example, if active health checks monitor the **/health** URL, but actual clients are getting errors on the **/cart** URL, HAProxy will detect that.

Beware that the active health checks will revive the server sooner or later, even if the **/cart** URL is still malfunctioning. One way to keep an unhealthy server down for longer is to extend the active health check interval by setting the `rise` parameter higher. Another solution is to turn your passive health check into a full blown circuit breaker by adding the `slowstart` parameter, which works well for backend services. We show how to do that in the blog post [Circuit Breaking in HAProxy](https://www.haproxy.com/blog/circuit-breaking-haproxy/).

## [](https://www.haproxy.com/blog/how-to-enable-health-checks-in-haproxy/#agent-health-checks)Agent Health Checks

While actively polling servers and observing live traffic are great ways to detect failures, it doesn’t give you a rich sense of a server’s overall state. For example, you can’t easily tell how much CPU load is being placed on it or if it’s running dangerously low on disk space.

With HAProxy, you can communicate with an _external agent_, which is software running on the server that’s separate from the application being load balanced. Since the agent has full access to the system, it can check the machine’s vitals more closely.

Check the [sample project in GitHub](https://github.com/haproxytechblog/haproxy-agent-check-example) to see a working example.

External agents can do more than just respond back with a binary _up_ or _down_ status. They can send signals to HAProxy that update its state, such as:

-   mark the server as up or down
-   put the server into maintenance mode
-   change the amount of traffic flowing to the server
-   increase or decrease the maximum number of clients that can connect concurrently

The agent will invoke an action when it detects a particular condition on the server. The communication protocol between the agent and HAProxy is simply ASCII text sent over a TCP connection, which makes it easy to write your own external agent program. The agent might send back any of the following (note that the end-of-line character, _\\n_, is required):

<table><tbody><tr><td><b>Agent sends back</b></td><td><b>Result</b></td></tr><tr><td>down\n</td><td>The server is put into the down state</td></tr><tr><td>up\n</td><td>The server is put into the up state</td></tr><tr><td>maint\n</td><td>The server is put into maintenance mode</td></tr><tr><td>ready\n</td><td>The server is taken out of maintenance mode</td></tr><tr><td>50%\n</td><td>The server’s weight is halved</td></tr><tr><td>maxconn:10\n</td><td>The server’ maximum connections is set to 10</td></tr></tbody></table>

On the HAProxy side, add an `agent-check` parameter to enable communication with the agent program.

```
backend webservers
  balance roundrobin
  server server1 192.168.50.2:80 check  weight 100  agent-check agent-inter 5s  agent-addr 192.168.50.2  agent-port 3000
```

There are a few other parameters shown here, so let’s describe them. Use `agent-inter` to set the interval of the checks. Set the `agent-addr` and `agent-port` parameters to the IP address and port where the agent is listening. Using an external agent gives you flexibility in how a server is checked and provides more ways to react. For example, instead of shutting off a server, you might decide to simply dial back the amount of traffic it receives.

## [](https://www.haproxy.com/blog/how-to-enable-health-checks-in-haproxy/#the-haproxy-enterprise-real-time-dashboard)The HAProxy Enterprise Real-time Dashboard

When you operate a non-trivial infrastructure, it soon becomes obvious that you need a consolidated view of your system. HAProxy Enterprise has a dashboard, called the [Real-time Dashboard](https://www.haproxy.com/documentation/hapee/latest/management/real-time-dashboard/), where you can observe the current status of all of your services.

![HAProxy Enterprise Real-time Dashboard](https://cdn.haproxy.com/wp-content/uploads/2021/08/health-checks-real-time-dashboard.png)

HAProxy Enterprise Real-time Dashboard

Having a central management dashboard makes health monitoring much easier. You can easily filter the list and each server can be enabled and disabled with a button click. You can also apply changes to batches of servers without needing to update each one individually.

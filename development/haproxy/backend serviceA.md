backend serviceA
   # Create storage for tracking client info
   stick-table  type string  size 1  expire 30s  store http_req_rate(10s),gpc0,gpc0_rate(10s),gpc1

   # Is the circuit broken?
   acl circuit_open be_name,table_gpc1 gt 0

   # Reject request if circuit is broken
   http-request return status 503 content-type "application/json" string "{ \"message\": \"Circuit Breaker tripped\" }" if circuit_open

   # Begin tracking requests
   http-request track-sc0 be_name

   # Count HTTP 5xx server errors
   http-response sc-inc-gpc0(0) if { status ge 500 }

   # Store the HTTP request rate and error rate in variables
   http-response set-var(res.req_rate) sc_http_req_rate(0)
   http-response set-var(res.err_rate) sc_gpc0_rate(0)

   # Check if error rate is greater than 50% using some math
   http-response sc-inc-gpc1(0) if { int(100),mul(res.err_rate),div(res.req_rate) gt 50 }
  
   server s1 192.168.0.10:80 check
   server s2 192.168.0.11:80 check
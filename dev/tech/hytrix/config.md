# Hystrix

## Configuration

There are 5 configurations of Hystrix we should notice:

- Timeout:                time.Duration
- MaxConcurrentRequests:  int
- RequestVolumeThreshold: uint64
- SleepWindow:            time.Duration
- ErrorPercentThreshold:  int

### Timeout

Default value: 1000 (ms)

This property sets the time in milliseconds after which the caller will observe a timeout and walk away from the command execution. When timeout occurs, the request is considered to be failed. In this case, Hystrix returns an Timeout error and performs fallback logic.

### MaxConcurrentRequests

Default value: 10

This controls the number of requests can run at the same time. It is basically the same as the size of thread pool managed by Hystrix.

### RequestVolumeThreshold

Default value: 20

A number of requests go through circuit breaker before Hystrix makes any decision whether to trip the circuit breaker. When a number of requests is lower than this value, decision will never be made. By this number, you can make sure that the circuit breaker starts its work on a certain throughput of requests.

### ErrorPercentThreshold

Default value: 50 (%)

This property serves as argument for the above decision. If the percent of failed requests is above this value, the circuit breaker will open, stopping subsequent requests from going further and the fallback logic will be perfomed.

### SleepWindow

Default value: 5000 (ms)

It controls the size of window of received requests. In order for the circuit breaker to work, all things must happend in the window. While in 5 seconds, if the circuit breaker receive at least 20 requests and the certain percentage of them fail then the circuit breaker will be open for next 5 seconds. After that time, the first request will be served to the downstream service. In case of success, the circuit breaker will open again.

## Methods

### Do

Prototype: ```func Do(name string, run runFunc, fallback fallbackFunc) error```  
Function: Runs your function in a synchronous manner, blocking until either your function succeeds or an error is returned, including hystrix circuit errors.

### Go

Prototye: ```func Go(name string, run runFunc, fallback fallbackFunc) chan error```  
Description: Calling `Go` is like lauching a goroutine, except you receive a channel of errors you can choose to monitor. `Go` runs your function while tracking the health of previous calls to it. If your function begins slowing down or failing repeatedly, we will block new calls to it for you to give the dependent service time to repair.

## Reference

- [hystrix-go](https://github.com/afex/hystrix-go)
- [What is Hystrix Timeout?](https://findanyanswer.com/what-is-hystrix-timeout)
- [Hystrix Circuit Breaker — How To Set It Up Properly](https://medium.com/@darek1024/hystrix-circuit-breaker-how-to-set-it-up-properly-84c75cfbe3ee)

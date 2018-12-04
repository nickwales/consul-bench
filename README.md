# consul-bench

consul-bench is a small tool to generate load on a Consul cluster by running blocking queries against a service.
It can automatically register service instances on startup to simulate a large number of nodes/instances.
Autoregistered instances can be configured to flap (go between critical and passing state) on configurable interval.

## Examples

### With an existing service

```
consul-bench -service my-service -watcher 300
```

Will run 300 `/v1/health/service/my-service?wait=10m&stale` blocking queries in parallel.

### Auto register N service instances

```
consul-bench -service my-fake-service -register 200 -flap-interval 10s -watcher 500
```

Will register 200 instances of "my-fake-service", make each instance flap every 10 seconds and run 500 blocking queries in parallel.

## Deregister

Registered instances are not deregistered when exiting however they will be deregistered after 6 * -flap-interval (or 20m if no -flap-interval is given) using `deregister_critical_service_after`.
If you want to immediatly deregister them, use `consul-bench -service my-service -deregister`. Note that this will deregister **all** instance of the service wether they were registered by consul-bench or not.

## Full options

```
Usage of consul-bench:
  -consul string
    	Consul address (default "127.0.0.1:8500")
  -deregister
    	Deregister all instances of -service
  -flap-interval duration
    	If -register is given, flap each instance between critical and passing state on given interval
  -register int
    	Register N -service instances
  -service string
    	Service to watch (default "srv")
  -token string
    	ACL token
  -watchers int
    	Number of concurrnet watchers on service (default 1)
```

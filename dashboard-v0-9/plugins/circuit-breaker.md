+++
draft = false
title = "Circuit Breaker"
date = 2014-07-29T09:31:36Z
[menu.dashboard_sidebar_v0_9]
    parent="plugins"
+++

Tyk has a built-in circuit breaker pattern as a path-based option. Our circuit breaker is threshold-based, so if `x%` of requests are failing then the circuit is tripped. When the circuit is tripped, then the gateway will stop *all* inbound requests to that service for a pre-defined period of time (a recovery time-period).

The circuit breaker will also emit an event which you can hook into to perform some corrective or logging action, the event is available in the webhooks section of the dashboard.

To enable The breaker, simply select the option from the plugins drop-down. You will need to define the optimal options for your circuit breaker in the form that appears.

![Plugins - Circuit Breaker](/imgs/circuit-breaker.png)

The options available to the circuit breaker are:

- **Trigger threshold percentage**: The percentage of requests that should fail before the breaker trips
- **Sample size**: The number of requests to sample
- **Return to service in (s)**: How long to take the service offline

### Service discovery

If you are using the service discovery option, every time the breaker trips, Tyk will attempt to refresh the node list.
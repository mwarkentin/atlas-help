---
title: "About Consul Alerts"
---

# About Consul Alerts

Consul can be connected to Atlas to automatically provide
alerts to several different [notification methods](/help/consul/alerts/notification-methods),
including Slack, email, HipChat and Pagerduty.

Alerts can be set at three thresholds:

- __Off__: No alerts of any kind will be recorded or sent
- __Critical Only__: Only alerts caused by a health check becoming 'critical'
will be recorded and sent to configured notification channels.
Recoveries will not be recorded or sent.
- __All__: Health checks that become warning, critical, or recover
to passing will be recorded and sent to configured notification
channels. This applies to all individual node and service checks,
or when 10% or more of a service across multiple nodes becomes unhealthy

The alert threshold can be set in the "Integrations" tab of an environment.

## Health Checks

A health check is considered to be application-level if it is associated with
a service. If not associated with a service, the check monitors the health
of the entire node.

Health checks trigger alerts by changing status on a Consul node. That status
change is seen by Atlas, when connected, and an associated alert is
recorded in Atlas and sent to any configured notification methods, like
email.

## Recoveries

When Atlas sees a check return to a `passing` status from a `critical`
status is treats this is a recovery, recording and showing it as
such in the UI, as well as alerting that a recovery occurred.

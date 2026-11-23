# FortiGate notes

FortiGate (FGT-HQ) runs FortiOS 8.0.1. No keys, passwords or pre-shared secrets are included in this repository.

## Syslog to Splunk

| Setting | Value |
|---|---|
| Status | enable |
| Server | the Splunk server on the lab management side |
| Mode and port | UDP 514 |
| Facility | local7 |
| Severity filter | information |

Traffic (forward and local), UTM and event logs are all forwarded. The filter enables forward, local, multicast, sniffer and ZTNA traffic, HTTP transactions and anomaly logs.

## DMZ policy

The policy that publishes the web server to the outside network has IPS and Web Filter profiles applied. The IPS sensor in use is named `protect_http_server`.

In the logs, a blocked SQL injection looks like this (shortened):

```
type="utm" subtype="ips" level="alert" severity="high"
action="dropped" service="HTTP"
attack="HTTP.URI.SQL.Injection" attackid=15621
profile="protect_http_server" direction="outgoing"
```

## Where to look in the GUI

| What | Location |
|---|---|
| Blocked attacks | Log & Report, Security Events, Intrusion Prevention |
| Firewall policies | Policy & Objects, Firewall Policy |
| IPS sensors | Security Profiles, Intrusion Prevention |
| Syslog settings | Log & Report, Log Settings |

Note: the Security Events view reads from memory in this lab, so it is cleared when the firewall restarts. Splunk keeps the history.

## Not published

The full configuration backup and the IPsec settings are left out on purpose because they contain secrets.

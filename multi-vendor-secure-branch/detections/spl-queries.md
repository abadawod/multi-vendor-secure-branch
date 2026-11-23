# Splunk detections

All searches below were run against the lab data and returned the results described. Time ranges are set to the last 30 days; adjust as needed.

FortiGate logs arrive as key=value syslog in `index=network`. Sysmon events arrive in `index=sysmon`.

## T1190 - Exploit Public-Facing Application (SQL injection)

The FortiGate IPS logs each blocked attempt as a UTM event. The destination appears as the internal server address because the address translation is applied before the IPS inspects the traffic.

```
index=network type=utm subtype=ips attack="*SQL*"
| stats count, values(action) as action, values(attack) as attack, min(_time) as first_seen, max(_time) as last_seen by srcip, dstip, dstport
| convert ctime(first_seen) ctime(last_seen)
```

Result in the lab: three attempts from the attacker address, attack `HTTP.URI.SQL.Injection`, action `dropped`.

## T1110 - Brute Force

The login brute force against the web application shows up as many short HTTP sessions from one source to the web server. In the lab, every brute-force run produced 30 to 32 sessions per minute, so a threshold of 20 per minute separates it from normal browsing.

```
index=network type=traffic subtype=forward dstport=80
| bin _time span=1m
| stats count by _time, srcip, dstip
| where count>=20
```

## T1046 - Network Service Discovery (port scan)

The scan targets the firewall's own address, so the dropped probes are logged as local traffic with action `deny`. The search counts distinct destination ports per source in five-minute windows.

```
index=network type=traffic subtype=local action=deny
| bin _time span=5m
| stats dc(dstport) as ports, count as denied_attempts by _time, srcip, dstip
| where ports>=15
| sort - _time
```

Result in the lab: about 997 to 998 distinct ports and roughly 1,994 to 1,996 denied attempts per scan.

## Sysmon telemetry per host

```
index=sysmon
| timechart span=1h count by host
```

## Saved alert

A scheduled alert named `ALERT - Port Scan Detected` runs every hour at minute 15 over the previous 60 minutes. The same logic as the T1046 search above can be saved as an alert from the search page.

## Notes on false positives

- The brute-force threshold fits this lab. In production it would need tuning per application, and a source that is a load balancer or a scanner should be excluded.
- The port-scan threshold of 15 ports in five minutes is low on purpose for a small lab.

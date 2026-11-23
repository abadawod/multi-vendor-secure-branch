# Splunk Universal Forwarder (DC01 and BR-WIN11)

The same configuration is used on both Windows machines. It collects only the Sysmon channel and sends it to the `sysmon` index.

Place `inputs.conf` here and restart the service:

```
C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf
```

```
Restart-Service SplunkForwarder
```

The forwarder points at the Splunk server on TCP 9997.

## Important: service account

The forwarder service must run as `LocalSystem`, otherwise it cannot read the Sysmon channel and the log shows `errorCode=5` (access denied). To change it:

```
sc.exe config SplunkForwarder obj= "LocalSystem"
```

```
Restart-Service SplunkForwarder
```

## Sysmon

Sysmon was installed on both machines with the SwiftOnSecurity configuration.

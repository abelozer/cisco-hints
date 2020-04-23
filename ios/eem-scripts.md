# EEM Scripts

## Deny switchport trunk allowed vlan N

Applet allows only `switchport trunk allowed vlan add|remove VLAN_ID`but denies `switchport trunk allowed vlan VLAN_ID` thus it can help you with this often mistake.

```csharp
event manager applet DENY-SWITCHPORT-TRUNK-ALLOWED-VLAN
 event cli pattern "switchport trunk allowed vlan [0-9]+" sync no skip yes
 action 1.0 puts "==> Command execution aborted. Add or remove keywords are required."
```

{% hint style="warning" %}
If there's another cli applet which uses `skip no` for the same command, then `skip yes` does not work.
{% endhint %}

## Action in interface shutdown example

```csharp
event manager applet monitorShutdown
 description “Monitors interface shutdown.”
 event cli match “conf t; interface *; shutdown”
 action 1.0 cli show interface e 3/1
 copy running-config startup-config
```

## Watch Loopback state

```csharp
event manager applet WatchLo0
 event syslog pattern "Interface Loopback0.* down" period 1
 action 2.0 cli command "enable"
 action 2.1 cli command "config t"
 action 2.2 cli command "interface lo0"
 action 2.3 cli command "no shutdown"
 action 3.0 syslog msg "Interface Loopback0 was brought up via EEM"
```

## Catch CLI commands and log them

```csharp
event manager applet catchall
 event cli pattern ".*" sync no skip no
 action 1 syslog msg "$_cli_msg"
```


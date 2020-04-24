---
description: Example of configuration groups which can be inherited by configuration items
---

# Flexible CLI

{% hint style="danger" %}
All templates are provided as an examples only. You should verify them before applying.
{% endhint %}

Flexible CLI uses groups and templates which can be applied to the configuration. The inherited group configuration is not shown with simple **show running-config** but there's a special keyword **inheritance**. If you also add **details** you'll see commented lines _\#inherited from group_. This is not very common because you can't see configuration with a command you used to use. Flexible CLI helps to keep configuration standartised and this is a good reason to think about it at least.

## Create groups

First of all we're creating groups. Pay attention to the values in quotes. They represent basic regular expressions for matching some numbers. OSPF process id or if-number.

```csharp
group ospf
 router ospf '[0-9]'
  log adjacency changes
  nsf ietf
  auto-cost reference-bandwidth 100000
  address-family ipv4 unicast
  area '0'
   interface 'Loopback0'
    passive enable
   !
   interface 'Bundle-Ether.*'
    network point-to-point
   !
   interface 'GigabitEthernet.*'
    network point-to-point
   !
  !
 !
end-group
!
group drain
 router ospf '1'
  max-metric router-lsa
 !
end-group
!
group undrain
end-group
!
group interfaces
 cdp
 lldp
 !
 service unsupported-transceiver
 interface 'Gig.*'
  cdp
  mtu 9000
  load-interval 30
 !
end-group
```

## Create templates

Later we'll use these templates as well as groups for dynamic device configuration.

```bash
template drain (as)
!
route-policy DRAIN-BGP
  prepend as-path $as 5
end-policy
!
end-template
!
template undrain (as)
!
route-policy DRAIN-BGP
end-policy
!
end-template
```

## Apply groups

Apply groups globally.

```text
apply-group interfaces ospf undrain
```

At this moment configuration inherits the instructions from three groups.

## Create alias

You may ask why we need groups and templates called _drain_ and _undrain_. I'm going to show how we can use groups for dynamic device configuration. We'll need alias for this.

This alias uses one parameter _action_ for calling group & template name. For simplicity we call them identically.

```text
alias setbox (action) apply-group interfaces ospf $action; apply-template $action (1)
```

> **Warning** Apply-group rewrites itself. If you want to add one group more you have to list all of them.

## Use alias

Of course engineers should not do this with their hands. This work is dedicated for scripts.

```text
conf
 setbox drain|undrain
commit
```



## Other Templates

### Interfaces

Some default config for all interfaces:

```csharp
group NNI-Main
 interface 'Gi.*'
  ipv4 unreachables disable
  ipv6 unreachables disable
  carrier-delay up 10000 down 0
  dampening 1 1000 1500 4
 !
 interface 'TenG.*'
  ipv4 unreachables disable
  ipv6 unreachables disable
  carrier-delay up 10000 down 0
  dampening 1 1000 1500 4
 !
 interface 'FortyGigE.*'
  ipv4 unreachables disable
  ipv6 unreachables disable
  carrier-delay up 10000 down 0
  dampening 1 1000 1500 4
 !
 interface 'Bundle-Eth.*'
  ipv4 unreachables disable
  ipv6 unreachables disable
  dampening 1 1000 1500 4
 !
 interface 'HundredGigE.*'
  ipv4 unreachables disable
  ipv6 unreachables disable
  carrier-delay up 10000 down 00
  dampening 1 1000 1500 4
 !
end-group
```

### OSPF

Change network type to point-to-point to all OSPF interface in area 0:

```csharp
group OSPF-P2P
 router ospf '.*'
  area '0.*'
   interface 'Gi.*'
    network point-to-point
   !
   interface 'TenG.*'
    network point-to-point
   !
   interface 'Bundle-Eth.*'
    network point-to-point
   !
  !
 !
end-group
```

Set max-metric:

```csharp
group MAX-METRIC-OSPF
 router ospf '.*'
  max-metric router-lsa
  vrf '.*'
   max-metric router-lsa
  !
 !
end-group
```

Enable Segment Routing for OSPF:

```csharp
group SR-OSPF-AREA0
 mpls traffic-eng
 !
 router ospf '.*'
  segment-routing mpls
  area '0.*'
   mpls traffic-eng
  !
  mpls traffic-eng router-id Loopback0
 !
end-group
```

Enable TI-LFA for all OSPF interface in Area 0:

```csharp
group OSPF-TI-LFA-AREA0
 router ospf '.*'
  area '0.*'
   fast-reroute per-prefix
   fast-reroute per-prefix ti-lfa enable
  !
 !
end-group
```

Enable TI-LFA for OSPF per interface:

```csharp
group OSPF-TI-LFA-PER-INTERFACE
 router ospf '.*'
  area '0.*'
   interface 'Gi.*'
    fast-reroute per-prefix
    fast-reroute per-prefix ti-lfa enable
   !
   interface 'TenG.*'
    fast-reroute per-prefix
    fast-reroute per-prefix ti-lfa enable
   !
   interface 'Bundle-Eth.*'
    fast-reroute per-prefix
    fast-reroute per-prefix ti-lfa enable
   !
  !
 !
end-group
```

Enable TI-LFA for OSPF process:

```csharp
group OSPF-TI-LFA-PROCESS
 router ospf '.*'
  fast-reroute per-prefix
  fast-reroute per-prefix ti-lfa enable
 !
end-group
```

### BGP

Enable BGP Graceful Maintenance Mode for all BGP neighbours:

```csharp
group BGP-GRACEFUL-MAINTENANCE
 router bgp '[0-9]*'
  neighbor-group '([^\s]+)'
   graceful-maintenance
    local-preference 20
    as-prepends 3
   !
  !
  neighbor '[0-9]*\.[0-9]*\.[0-9]*\.[0-9]*'
   graceful-maintenance
    local-preference 20
    as-prepends 3
   !
  !
  vrf '([^\s]+)'
   neighbor '[0-9]*\.[0-9]*\.[0-9]*\.[0-9]*'
    graceful-maintenance
     local-preference 20
     as-prepends 3
    !
   !
  !
 !
end-group
```

### ISIS

Enable Segment Routing for ISIS Level-2:

```csharp
group SR-ISIS-LEVEL-2-ONLY
 router isis '.*'
  address-family ipv4 unicast
   metric-style wide
   mpls traffic-eng level-2-only
   mpls traffic-eng router-id Loopback0
   segment-routing mpls
  !
 !
 mpls traffic-eng
 !
end-group
```

Enable ISIS TI-LFA:

```csharp
group ISIS-TI-LFA
 router isis '.*'
  interface 'GigabitEthernet.*'
   address-family ipv4 unicast
    fast-reroute per-prefix
    fast-reroute per-prefix ti-lfa
   !
  !
 !
end-group
```


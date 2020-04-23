# Flexible CLI

{% hint style="danger" %}
All templates are provided as an example. You should verify them before applying.
{% endhint %}

### Interfaces

```text
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

Change network type to point-to-point to all OSPF interface in area 0

```text
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

Set max-metric

```text
group MAX-METRIC-OSPF
 router ospf '.*'
  max-metric router-lsa
  vrf '.*'
   max-metric router-lsa
  !
 !
end-group
```

Enable Segment Routing for OSPF

```text
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

Enable TI-LFA for all OSPF interface in Area 0

```text
group OSPF-TI-LFA-AREA0
 router ospf '.*'
  area '0.*'
   fast-reroute per-prefix
   fast-reroute per-prefix ti-lfa enable
  !
 !
end-group
```

Enable TI-LFA for OSPF per interface

```text
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

Enable TI-LFA for OSPF process

```text
group OSPF-TI-LFA-PROCESS
 router ospf '.*'
  fast-reroute per-prefix
  fast-reroute per-prefix ti-lfa enable
 !
end-group
```

### BGP

Enable BGP Graceful Maintenance Mode for all BGP neighbours

```text
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

Enable Segment Routing for ISIS Level-2

```text
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

Enable ISIS TI-LFA

```text
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


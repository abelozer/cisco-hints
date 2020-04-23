# BGP Next Hop Tracking

By default IOS uses summary or default route to resolve next-hop. To prevent this it's better to limit the lookups by route-map.

## Template

```text
router bgp {{ ASN }}
 address-family ipv4
  bgp nexthop route-map {{ ROUTE_MAP_NAME }}
  bgp nexthop trigger delay {{ NHT_TRIGGER_DELAY }}
 address-family vpnv4
  bgp nexthop route-map {{ ROUTE_MAP_NAME }}
  bgp nexthop trigger delay {{ NHT_TRIGGER_DELAY }}
!
route-map {{ ROUTE_MAP_NAME }} permit 10
 match ip address prefix-list {{ PREFIX_LIST_NAME }}
route-map {{ ROUTE_MAP_NAME }} permit 20
 match source-protocol connected
!
ip prefix-list {{ PREFIX_LIST_NAME }} seq 5 permit {{ LOOPBACK_SUBNET }}/{{ LOOPBACK_SUBNET_MASK }} ge 32
```

### Variables

```yaml
---
NHT_TRIGGER_DELAY: 1
ROUTE_MAP_NAME: RM-NHT
PREFIX_LIST_NAME: LOOPBACKS
LOOPBACK_SUBNET: 10.0.0.0
LOOPBACK_SUBNET_MASK: 255.255.255.0
```


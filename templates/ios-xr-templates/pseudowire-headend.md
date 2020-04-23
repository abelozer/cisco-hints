# Pseudowire Headend

## Template

```text
generic-interface-list {{ GENERIC_IFACE_LIST_NAME | default('CORE') }}
{% for interface in item.nni %}
{# sub-interfaces are not allowed in GIL #}
 interface {{ interface.name }}
{% endfor %}
!
interface PW-Ether{{ IF_NUMBER }}
 vrf {{ VRF_NAME }}
 ipv4 address {{ IP_ADDRESS }} {{ MASK }}
 attach generic-interface-list {{ GENERIC_IFACE_LIST_NAME }}
!
l2vpn
 pw-class pwhe-mpls
  encapsulation mpls
 !
 xconnect group PWHE
  p2p {{ P2P_NAME }}
   interface PW-Ether{{ IF_NUMBER }}
   neighbor {{ NEIGHBOR_IP }} pw-id {{ PW_ID }}
    pw-class pwhe-mpls
```

## Verification

* `show interface pw-ether`
* `show interface pw-ether  accounting`
* `show l2vpn xconnect group detail`
* `show l2vpn pwhe interface pw-ether detail`


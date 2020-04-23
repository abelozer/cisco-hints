---
description: Netconf configuration template for IOS XR
---

# Netconf Template

## Template

{% tabs %}
{% tab title="Cisco IOS XR" %}
```erlang
netconf-yang agent ssh
!
ssh server v2
ssh server netconf vrf {{ VRF_NAME }} ipv4 access-list {{ NETCONF_ACL_NAME }}
ssh server netconf port {{ NETCONF_PORT_NUMBER | default('830') }}
ssh timeout 120
```
{% endtab %}
{% endtabs %}

## Example

```text
ipv4 access-list ACL-NETCONF
 10 permit ipv4 host 192.168.1.1 any
! 
netconf-yang agent ssh
!
ssh server v2
ssh server netconf vrf default ipv4 access-list ACL-NETCONF
```

## Show commands

```text
show netconf-yang statistics
show netconf-yang clients
```


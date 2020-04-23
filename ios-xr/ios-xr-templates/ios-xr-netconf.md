---
description: Generic netconf configuration for IOS XR.
---

# IOS XR Netconf

## Template

{% tabs %}
{% tab title="Cisco IOS XR" %}
```text
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
netconf-yang agent ssh
!
ssh server v2
ssh server netconf vrf default
```

## Show commands

```text
show netconf-yang statistics
show netconf-yang clients
```


---
description: SR IGP templates for IOS XR
---

# IGP Configuration Template

{% hint style="danger" %}
The following template was originally designed for IOS XR 7.1.x.
{% endhint %}

## Jinja2 IGP SR Template

{% hint style="info" %}
A default SRGB for IOS XR is 16000 – 23999.
{% endhint %}

{% tabs %}
{% tab title="ISIS" %}
```Django
segment-routing global-block {{ SRGB_START | default("16000") }} {{ SRGB_END | default("23999") }}
!
router isis {{ ISIS_PROCESS | default("1") }}
 address-family ipv4 unicast
  router-id {{ RID_IFACE | default("Loopback0") }}
  metric-style wide
  mpls traffic-eng {{ ISIS_LEVEL | default("level-2-only") }}
  mpls traffic-eng router-id {{ RID_IFACE | default("Loopback0") }}
  segment-routing mpls
{% raw %}
{%- if MICROLOOP_AVOIDANCE %}
  microloop avoidance segment-routing
{%- endif %}
{%- if MICROLOOP_AVOIDANCE_DELAY %}
  microloop avoidance rib-update-delay {{ MICROLOOP_AVOIDANCE_DELAY }}
{%- endif %}
{%- if MAPPING_SERVER %}
  segment-routing prefix-sid-map advertise-local
{%- endif %}
 !
 interface {{ RID_IFACE | default("Loopback0") }}
  address-family ipv4 unicast
   prefix-sid index {{ INDEX }}
 !
{%- for IFACE in NNI %}
 interface {{ IFACE.NAME }}
{%-   if TI_LFA %}
  address-family ipv4 unicast
   fast-reroute per-prefix
   fast-reroute per-prefix ti-lfa
{%-   endif %}
 !
{%- endfor %}
{% endraw %}
!
mpls traffic-eng
```
{% endtab %}
{% endtabs %}

## Sample Variables

`MICROLOOP_AVOIDANCE_DELAY` specifies the amount of time the node uses the microloop avoidance policy before updating its forwarding table. The delay-time is in milliseconds. The range is from 1-60000. The default value is 5000.

```yaml
---
ISIS_PROCESS: 'CORE'
MICROLOOP_AVOIDANCE: True
# MICROLOOP_AVOIDANCE_DELAY: 5000 # default value is 5000
TI_LFA_PROCESS: True
TI_LFA: True
INDEX: 1
NNI:
  - NAME: 'Te0/0/0/1'
  - NAME: 'Te0/0/0/2'
```

## Notes

MPLS traffic engineering functionality is required on SR node. The node advertises the traffic engineering link attributes in IGP which populate the traffic engineering database (TED) on the head-end. The SR-TE head-end requires the TED to calculate and validate the path of the SR-TE policy.

## Links

[Segment Routing Configuration Guide for Cisco ASR 9000 Series Routers, IOS XR Release 7.1.x](https://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k-r7-1/segment-routing/configuration/guide/b-segment-routing-cg-asr9000-71x.html)

## Todo

* [ ] Add flexalgo support to the templates

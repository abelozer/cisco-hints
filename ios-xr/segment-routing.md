# Segment Routing

### Jinja2 ISIS SR Template

{% hint style="info" %}
A default SRGB for IOS XR is 16000 – 23999.
{% endhint %}

{% tabs %}
{% tab title="ISIS" %}
```julia
{% if SRGB_START and SRGB_END %}
segment-routing global-block {{ SRGB_START }} {{ SRGB_END }}
!
{% endif %}
router isis {{ ISIS_PROCESS | default("1") }}
 address-family ipv4 unicast
  metric-style wide
  mpls traffic-eng {{ ISIS_LEVEL | default("level-2-only") }}
  mpls traffic-eng router-id {{ RID_IFACE | default("Loopback0") }}
  segment-routing mpls
{% if MICROLOOP_AVOIDANCE %}
  microloop avoidance segment-routing
{% endif %}
{% if MICROLOOP_AVOIDANCE_DELAY %}
  microloop avoidance rib-update-delay {{ MICROLOOP_AVOIDANCE_DELAY }}
{% endif %}
{% if MAPPING_SERVER %}
  segment-routing prefix-sid-map advertise-local
{% endif %}
 !
 interface {{ RID_IFACE | default("Loopback0") }}
  address-family ipv4 unicast
   prefix-sid index {{ INDEX }}
 !
{% for IFACE in NNI %}
 interface {{ IFACE.NAME }}
{%   if TI_LFA %}
  address-family ipv4 unicast
   fast-reroute per-prefix
   fast-reroute per-prefix ti-lfa
{%   endif %}
 !
{% endfor %}
!
mpls traffic-eng
```
{% endtab %}

{% tab title="OSPF" %}
```julia
{% if SRGB_START and SRGB_END %}
segment-routing global-block {{ SRGB_START }} {{ SRGB_END }}
!
{% endif %}
router ospf {{ OSPF_PROCESS | default("1") }}
 segment-routing mpls
{% if MAPPING_SERVER %}
 segment-routing prefix-sid-map advertise-local
{% endif %}
{% if MICROLOOP_AVOIDANCE %}
 microloop avoidance segment-routing
{% endif %}
{% if MICROLOOP_AVOIDANCE_DELAY %}
 microloop avoidance rib-update-delay {{ MICROLOOP_AVOIDANCE_DELAY }}
{% endif %}
{% if TI_LFA_PROCESS %}
 fast-reroute per-prefix
 fast-reroute per-prefix ti-lfa
{% endif %}
 area 0
 {% if TI_LFA_AREA0 %}
  fast-reroute per-prefix
  fast-reroute per-prefix ti-lfa
 {% endif %}
  mpls traffic-eng
  !
  interface {{ RID_IFACE | default("Loopback0") }}
   prefix-sid index {{ INDEX }}
  !
{% for IFACE in NNI %}
{%   if IFACE.TI_LFA %}
  interface {{ IFACE.NAME }}
   fast-reroute per-prefix
   fast-reroute per-prefix ti-lfa
  !
{%   endif %}
{% endfor %}
 !
 mpls traffic-eng router-id {{ RID_IFACE | default("Loopback0") }}
!
mpls traffic-eng
```
{% endtab %}
{% endtabs %}

### YANG Variables

`MICROLOOP_AVOIDANCE_DELAY` specifies the amount of time the node uses the microloop avoidance policy before updating its forwarding table. The delay-time is in milliseconds. The range is from 1-60000. The default value is 5000.

{% tabs %}
{% tab title="ISIS" %}
```yaml
---
ISIS_PROCESS: 'CORE'
MICROLOOP_AVOIDANCE: True
TI_LFA_PROCESS: True
TI_LFA: True
INDEX: 1
NNI:
  - NAME: 'Te0/0/0/1'
  - NAME: 'Te0/0/0/2'
```
{% endtab %}

{% tab title="OSPF" %}
```yaml
---
MICROLOOP_AVOIDANCE_DELAY: 5000
```
{% endtab %}
{% endtabs %}

### MPLS TE

MPLS traffic engineering functionality is required on SR node. The node advertises the traffic engineering link attributes in IGP which populate the traffic engineering database \(TED\) on the head-end. The SR-TE head-end requires the TED to calculate and validate the path of the SR-TE policy.

## Links

[Segment Routing Configuration Guide for Cisco ASR 9000 Series Routers, IOS XR Release 6.3.x](https://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k-r6-3/segment-routing/configuration/guide/b-segment-routing-cg-asr9000-63x.html)


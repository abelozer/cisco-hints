# Segment Routing

## ISIS

### Jinja2 ISIS SR Template

{% hint style="info" %}
A default SRGB for IOS XR is 16000 – 23999.
{% endhint %}

```text
{% if SRGB_START and SRGB_END %}
segment-routing global-block {{ SRGB_START }} {{ SRGB_END }}
!
{% endif %}
router isis {{ ISIS_PROCESS | default("1") }}
 address-family ipv4 unicast
{# TODO: verify metric-style command #}
  metric-style wide
{# Enables IGP traffic engineering functionality. #}
  mpls traffic-eng {{ ISIS_LEVEL | default("level-2-only") }}
{# Sets the traffic engineering loopback interface. #}
  mpls traffic-eng router-id {{ RID_IFACE | default("Loopback0") }}
{# Segment routing is enabled by the following actions:
    * MPLS forwarding is enabled on all interfaces where IS-IS is active.
    * All known prefix-SIDs in the forwarding plain are programmed, with the
      prefix-SIDs advertised by remote routers or learned through local or
      remote mapping server.
    * The prefix-SIDs locally configured are advertised.
#}
  segment-routing mpls
{% if MICROLOOP_AVOIDANCE %}
{# Enables Segment Routing Microloop Avoidance.
   Need to be carefull because a point of local repair needs to set two labels.
#}
  microloop avoidance segment-routing
{% endif %}
{% if MICROLOOP_AVOIDANCE_DELAY %}
{# Specifies the amount of time the node uses the microloop avoidance policy
before updating its forwarding table. The delay-time is in milliseconds. The
range is from 1-60000. The default value is 5000. #}
  microloop avoidance rib-update-delay {{ MICROLOOP_AVOIDANCE_DELAY }}
{% endif %}
{% if MAPPING_SERVER %}
{# Client is enabled by default from 6.1.1 #}
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
{# Enables traffic engineering funtionality on the node. The node advertises
the traffic engineering link attributes in IGP which populates the traffic
engineering database (TED) on the head-end. The SR-TE head-end requires the
TED to calculate and validate the path of the SR-TE policy. #}
!
mpls traffic-eng
```

### YANG Variables

```yaml
---
ISIS_PROCESS: 'CORE'
MICROLOOP_AVOIDANCE: True
TI_LFA: True
INDEX: 1
NNI:
  - NAME: 'Te0/0/0/1'
  - NAME: 'Te0/0/0/2'
```

## Links

[Segment Routing Configuration Guide for Cisco ASR 9000 Series Routers, IOS XR Release 6.3.x](https://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k-r6-3/segment-routing/configuration/guide/b-segment-routing-cg-asr9000-63x.html)


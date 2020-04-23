# ASR1k CPU Protection

## SPD

```text
ARP -> Input queue
BGP -> Headroom
HSRP -> Extended Headroom
OSPF -> Extended Headroom
IP Pre0 -> Input queue
IP Pre6 -> Headroom
```

## Implementation of CoPP in ASR1000

1. Internal interfaces in ASR1000:
   * Punt/inject interface: traffic between RP and FP
   * Recycle interface: control traffic handled by FP directly
2. Implementation relies heavily on QoS feature
   * CoPP is implemented as QoS on the punt/inject interface and recycle interface.
3. GPP is an internal ASR1k feature:
   * Designed to protect RP by enforcing a default policer on punt traffic \(150K pps and a burst of 2288 packets with average packet size of 1KB\).
   * Always on and not configurable.
   * Enforced after CoPP has been applied on ingress traffic.

## ASR1k CPU protection order

1. CoPP works before PCPP.
2. Per-cause Punt Policers work by default \(`sh pl software punt-policer`\)
3. Global Punt Policer

{% hint style="info" %}
Cisco Internal: you can check EDCS-703796 and EDCS-904674 for more details.
{% endhint %}

## Related CLI

* `show int  switching`
* `show ip spd`
* `show platform hardware qfp active statistics drop`
* `show platform software infrastructure punt [statistics]`

Following commands show CoPP programmed in HW:

* `show platform hardware qfp active feature qos all output all`
* `show platform hardware qfp active feature qos all input all`
* `show platform hardware qfp active feature qos all input interface internal0/0/rp:0`
* `show platform hardware qfp active feature qos all input interface internal0/0/rp:1`
* `show platform hardware qfp active feature qos all input interface internal0/0/recycle:0`
* `show platform hardware qfp active feature qos all output interface internal0/0/rp:0`
* `show platform hardware qfp active feature qos all output interface internal0/0/rp:1`
* `show platform hardware qfp active feature qos all output interface internal0/0/recycle:0`

In new SW:

* `show platform hardware pp active infrastructure pi npd rx policer`
* `show platform hardware pp active feature qos policer cpu stats 1`
* `show platform hardware pp active feature qos policer cpu stats 0`
* `show platform hardware pp active feature qos policer cpu 3 0`

## Links

1.[Punt Policing and Monitoring](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_plcshp/configuration/xe-16/qos-plcshp-xe-16-book/qos-plcshp-punt-police-monitor.html)


# Cat6800 Control-Plane Policy

## ACLs

```csharp
ip access-list extended acl-copp-critical
 remark *** OSPF ***
 permit ospf any any
 remark *** LDP ***
 permit tcp any any eq 646
 permit udp any any eq 646
 permit tcp any eq 646 any
 permit udp any eq 646 any
 remark *** EIGRP ***
 permit eigrp any any
 remark *** BGP ***
 permit tcp any any eq bgp
 permit tcp any eq bgp any
 remark *** BFD ***
 permit udp any any eq 3785
 permit udp any any eq 3784

ip access-list extended acl-copp-netapp
 remark *** HSRP v1 & v2 ***
 permit udp any host 224.0.0.2 eq 1985
 permit udp any host 224.0.0.102 eq 1985
 remark *** DHCP ***
 permit udp host 0.0.0.0 host 255.255.255.255 eq bootps

ip access-list extended acl-copp-mgmt
 remark *** Telnet ***
 permit tcp {{ NOC_and_NMS }} any eq telnet
 permit tcp {{ NOC_and_NMS }} eq telnet any established
 remark *** SSH ***
 permit tcp {{ NOC_and_NMS }} any eq 22
 permit tcp {{ NOC_and_NMS }} eq 22 any established
 remark *** SNMP ***
 permit udp {{ NOC_and_NMS }} any eq snmp
 remark *** DNS ***
 permit udp {{ DNS_SERVER }} eq domain any
 remark *** Tacacs+ ***
 permit tcp host {{ TACACS_SERVER_1 }} eq tacacs any established
 permit tcp host {{ TACACS_SERVER_2 }} eq tacacs any established
 remark *** ICMP from NOC ***
 permit icmp {{ NOC_and_NMS }} any
 remark *** NTP ***
 permit udp {{ NTP_SERVER_1 }} any eq ntp
 permit udp {{ NTP_SERVER_1 }} eq ntp any
 permit udp {{ NTP_SERVER_2 }} any eq ntp
 permit udp {{ NTP_SERVER_2 }} eq ntp any

ip access-list extended acl-copp-bulk
 remark *** TFTP ***
 permit udp host {{ TFTP_SERVER }} gt 1024 any
 remark *** FTP Active & Passive mode ***
 permit tcp any eq ftp any established
 permit tcp any eq ftp-data any
 permit tcp any gt 1024 any established

ip access-list extended acl-copp-monitoring
 permit icmp any any echo
 permit icmp any any echo-reply
 permit icmp any any ttl-exceeded
 permit icmp any any packet-too-big
 permit icmp any any port-unreachable
 permit icmp any any unreachable

ip access-list extended acl-copp-known-undesirable
 permit tcp any any fragments
 permit udp any any fragments
 permit icmp any any fragments
 permit ip any any fragments
 permit udp any any eq 1434
 permit udp any any eq snmptrap

ip access-list extended acl-copp-reactive-undesirable

ip access-list extended acl-copp-catch-all
 permit tcp any any
 permit udp any any
 permit icmp any any
 permit ip any any
```

## Class-maps

```text
class-map match-any class-copp-reactive-undesirable
 match access-group name acl-copp-reactive-undesirable

class-map match-any class-copp-known-undesirable
 match access-group name acl-copp-known-undesirable

class-map match-any class-copp-critical
 match access-group name acl-copp-critical

class-map match-all class-copp-netapp
 match access-group name acl-copp-netapp

class-map match-all class-copp-mgmt
 match access-group name acl-copp-mgmt

class-map match-all class-copp-bulk
 match access-group name acl-copp-bulk

class-map match-all class-copp-monitoring
 match access-group name acl-copp-monitoring

class-map match-all class-copp-catch-all
 match access-group name acl-copp-catch-all
```

## Policy-map

{% hint style="danger" %}
Class `class-copp-known-undesirable` drops fragments. Check GRE fragmentation on a box. If there's no GRE this class can be dropped.
{% endhint %}

```text
policy-map policy-copp
 class class-copp-reactive-undesirable
  police rate 10 pps burst 1 packets
   conform-action drop
   exceed-action drop
 class class-copp-known-undesirable
  police rate 10 pps burst 1 packets
   conform-action drop
   exceed-action drop
 class class-copp-mcast-v4-data-on-routedPort
  police rate 10 pps burst 1 packets
   conform-action drop
   exceed-action drop
 class class-copp-mcast-v6-data-on-routedPort
  police rate 10 pps burst 1 packets
   conform-action drop
   exceed-action drop
 class class-copp-icmp-redirect-unreachable
  police rate 100 pps burst 10 packets
   conform-action transmit
   exceed-action drop
 class class-copp-ucast-rpf-fail
  police rate 100 pps burst 10 packets
   conform-action transmit
   exceed-action drop
 class class-copp-vacl-log
  police rate 2000 pps burst 1 packets
   conform-action transmit
   exceed-action drop
 class class-copp-mcast-punt
  police rate 1000 pps burst 256 packets
   conform-action transmit
   exceed-action drop
 class class-copp-mcast-copy
  police rate 1000 pps burst 256 packets
   conform-action transmit
   exceed-action drop
 class class-copp-ip-connected
  police rate 1000 pps burst 256 packets
   conform-action transmit
   exceed-action drop
 class class-copp-ipv6-connected
  police rate 1000 pps burst 256 packets
   conform-action transmit
   exceed-action drop
 class class-copp-match-pim-data
  police rate 1000 pps burst 1000 packets
   conform-action transmit
   exceed-action drop
 class class-copp-match-pimv6-data
  police rate 1000 pps burst 1000 packets
   conform-action transmit
   exceed-action drop
 class class-copp-match-mld
  police rate 10000 pps burst 10000 packets
   conform-action set-discard-class-transmit 48
   exceed-action transmit
 class class-copp-match-igmp
  police rate 10000 pps burst 10000 packets
   conform-action set-discard-class-transmit 48
   exceed-action transmit
 class class-copp-match-ndv6
  police rate 1000 pps burst 1000 packets
   conform-action set-discard-class-transmit 48
   exceed-action drop
 class class-copp-mtu-fail
  police rate 1000 pps burst 10 packets
   conform-action transmit
   exceed-action drop
 class class-copp-ttl-fail
  police rate 1000 pps burst 10 packets
   conform-action transmit
   exceed-action drop
 class class-copp-unknown-protocol
  police rate 100 pps burst 10 packets
   conform-action transmit
   exceed-action drop
 class class-copp-unsupp-rewrite
  police rate 100 pps burst 10 packets
   conform-action transmit
   exceed-action drop
 class class-copp-critical
  police rate 10000000 bps burst 100000 bytes
   conform-action transmit
   exceed-action drop
 class class-copp-netapp
  police rate 10000000 bps burst 100000 bytes
   conform-action transmit
   exceed-action drop
 class class-copp-mgmt
  police rate 10000000 bps burst 100000 bytes
   conform-action transmit
   exceed-action drop
 class class-copp-bulk
  police rate 50000000 bps burst 100000 bytes
   conform-action transmit
   exceed-action drop
 class class-copp-monitoring
  police rate 5000000 bps burst 100000 bytes
   conform-action transmit
   exceed-action drop
 class class-copp-broadcast
  police rate 5000 pps burst 1000 packets
   conform-action transmit
   exceed-action drop
 class class-copp-catch-all
  police rate 1200000 bps burst 100000 bytes
   conform-action transmit
   exceed-action drop
 class class-default
  police rate 1400000 bps burst 100000 bytes
   conform-action transmit
   exceed-action drop
```

## Apply policy

```text
platform qos police distributed strict
!
control-plane
 service-policy input policy-copp
```


# C7600 Control-Plane Policy

## ACLs

```text
ip access-list extended ACL-CPP-CRITICAL
 remark --- OSPF
 permit ospf any any
 remark --- LDP
 permit udp any eq 646 any
 permit udp any any eq 646
 permit tcp any eq 646 any
 permit tcp any any eq 646
 remark --- BGP
 permit tcp any any eq bgp
 permit tcp any eq bgp any
 remark --- BFD ---
 permit udp any any eq 3785
 permit udp any any eq 3784
 remark --- PIM
 permit pim any any
 remark --- PIM-AUTORP
 permit udp any eq pim-auto-rp any
 permit udp any any eq pim-auto-rp
 remark --- MSDP
 permit tcp any any eq 639
 permit tcp any eq 639 any gt 1024 established
 remark --- HSRP
 permit udp any host 224.0.0.2 eq 1985
 permit udp any host 224.0.0.102 eq 1985
 remark --- DHCP-Reply
 permit udp host {{ DHCP_SERVER_IP_ADDRESS }} eq bootps any eq bootps

ip access-list extended ACL-CPP-DHCPREQUEST
 remark --- DHCP-REQUEST
 permit udp host 0.0.0.0 host 255.255.255.255 range bootps bootpc

ip access-list extended ACL-CPP-FILEMANAGEMENT
 remark --- FTP & TFTP
 permit tcp host {{ IP_ADDRESS }} eq ftp any established
 permit tcp host {{ IP_ADDRESS }} eq ftp-data any
 permit tcp host {{ IP_ADDRESS }} gt 1024 any established
 permit udp host {{ TFTP_SERVER_IP_ADDRESS }} any

ip access-list extended ACL-CPP-IGMP
 remark --- IGMP
 permit igmp any any

ip access-list extended ACL-CPP-MANAGEMENT
 remark --- TELNET
 permit tcp {{ SUBNET }} {{ MASK }} any eq telnet
 permit tcp {{ SUBNET }} {{ MASK }} eq telnet any established
 permit tcp host {{ IP_ADDRESS }} any eq telnet
 permit tcp host {{ IP_ADDRESS }} eq telnet any established
 remark --- SNMP
 permit udp host {{ SNMP_SERVER_IP_ADDRESS }} any eq snmp
 remark --- NTP
 permit udp host {{ NTP_SERVER_IP_ADDRESS }} any eq ntp
 remark --- TACACS+
 permit tcp host {{ TACACS_IP_ADDRESS }} eq tacacs any established

 remark --- DNS
 permit udp host {{ DNS_SERVER_IP_ADDRESS }} eq domain any

ip access-list extended ACL-CPP-MATCHANY
 remark --- LINK-LOCAL-MCAST
 permit ip any 224.0.0.0 0.0.0.255
 permit ip any 224.0.1.0 0.0.0.255
 remark --- SPECIFIC PROTOCOLS
 permit tcp any any
 permit udp any any
 permit icmp any any
 permit ip any any

ip access-list extended ACL-CPP-MONITORING
 remark --- ICMP-TRACEROUTE
 permit icmp any any echo-reply
 permit icmp any any echo
 permit icmp any any ttl-exceeded
 permit icmp any any port-unreachable
 permit icmp any any packet-too-big
 permit icmp any any unreachable

ip access-list extended ACL-CPP-UNDESIRABLE
 remark --- FRAGMENTS
 permit icmp any any fragments
 permit udp any any fragments
 permit tcp any any fragments
 permit ip any any fragments
 remark --- MSSQL
 permit udp any any eq 1434
 remark -- TCP RST
 permit tcp any any eq 639 rst
 permit tcp any any eq bgp rst
```

## Class-maps

```text
class-map match-all CLASS-CPP-MONITORING
  match access-group name ACL-CPP-MONITORING
class-map match-all CLASS-CPP-CRITICAL
  match access-group name ACL-CPP-CRITICAL
class-map match-all CLASS-CPP-MATCHANY
  match access-group name ACL-CPP-MATCHANY
class-map match-all CLASS-CPP-FILEMANAGEMENT
  match access-group name ACL-CPP-FILEMANAGEMENT
class-map match-all CLASS-CPP-MANAGEMENT
  match access-group name ACL-CPP-MANAGEMENT
class-map match-all CLASS-CPP-IGMP
  match access-group name ACL-CPP-IGMP
class-map match-all CLASS-CPP-DHCPREQUEST
  match access-group name ACL-CPP-DHCPREQUEST
class-map match-all CLASS-CPP-UNDESIRABLE
  match access-group name ACL-CPP-UNDESIRABLE
```

## Policy-map

```text
policy-map PM-COPP
  class CLASS-CPP-UNDESIRABLE
    police cir 32000 bc 3200 conform-action drop exceed-action drop
  class CLASS-CPP-CRITICAL
    police cir 960000 bc 96000 conform-action transmit exceed-action transmit
  class CLASS-CPP-MANAGEMENT
    police cir 640000 bc 64000 conform-action transmit exceed-action drop
  class CLASS-CPP-FILEMANAGEMENT
    police cir 6400000 bc 640000 conform-action transmit exceed-action drop
  class CLASS-CPP-MONITORING
    police cir 480000 bc 48000 conform-action transmit exceed-action drop
  class CLASS-CPP-DHCPREQUEST
    police cir 1000000 bc 31250 be 31250
     conform-action transmit
     exceed-action transmit
     violate-action drop
  class CLASS-CPP-IGMP
    police cir 512000 bc 16000 be 16000
     conform-action transmit
     exceed-action transmit
     violate-action drop
  class CLASS-CPP-MATCHANY
    police 96000 3000 3000 conform-action transmit exceed-action transmit
  class class-default
    police cir 480000 bc 48000 conform-action transmit exceed-action transmit
```

## Attaching Policy

```text
control-plane
 service-policy input PM-COPP
```


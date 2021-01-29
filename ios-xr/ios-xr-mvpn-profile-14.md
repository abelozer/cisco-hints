# IOS XR mVPN Profile 14

{% hint style="info" %}
Make sure your software version supports mVPN Profile 14.
{% endhint %}

## Topology

![](../.gitbook/assets/mvpn-topology.png)

1. C-RP is configured on PE2: `21.21.21.21`. All other router have C-RP statically defined.

## Configuration

mVPN configuration is identical for all PEs.

{% hint style="info" %}
Only mVPN relevant configuration is listed below.
{% endhint %}

{% tabs %}
{% tab title="Egress PE1" %}
```aspnet
hostname PE1
!
vrf A
 vpn id 1:1
 address-family ipv4 unicast
  import route-target
   1:1
  !
  export route-target
   1:1
  !
 !
!
interface Loopback0
 ipv4 address 1.1.1.1 255.255.255.255
!
interface GigabitEthernet0/0/0/0
 description to GigabitEthernet0/0.CE1
 vrf A
 ipv4 address 192.168.1.1 255.255.255.0
!
interface GigabitEthernet0/0/0/1
 description to GigabitEthernet0/0/0/1.P
 ipv4 address 10.0.101.1 255.255.255.0
!
route-policy PASS
  pass
end-policy
!
route-policy mVPN-Profile-14
  set core-tree mldp-partitioned-p2mp
end-policy
!
router ospf 1
 router-id 1.1.1.1
 area 0
  mpls ldp auto-config
  interface Loopback0
   passive enable
  !
  interface GigabitEthernet0/0/0/1
   network point-to-point
  !
 !
!
router bgp 1
 bgp router-id 1.1.1.1
 address-family vpnv4 unicast
 !
 address-family ipv4 mvpn
 !
 neighbor 2.2.2.2
  remote-as 1
  update-source Loopback0
  address-family vpnv4 unicast
  !
  address-family ipv4 mvpn
  !
 !
 neighbor 3.3.3.3
  remote-as 1
  update-source Loopback0
  address-family vpnv4 unicast
  !
  address-family ipv4 mvpn
  !
 !
 vrf A
  rd auto
  address-family ipv4 unicast
   redistribute connected
  !
  address-family ipv4 mvpn
  !
  neighbor 192.168.1.2
   remote-as 2
   address-family ipv4 unicast
    route-policy PASS in
    route-policy PASS out
    as-override
   !
  !
 !
!
mpls ldp
 mldp
  address-family ipv4
  !
 !
 router-id 1.1.1.1
!
multicast-routing
 address-family ipv4
  interface Loopback0
   enable
  !
 !
 vrf A
  address-family ipv4
   mdt source Loopback0
   rate-per-route
   interface all enable
   accounting per-prefix
   bgp auto-discovery mldp
   !
   mdt partitioned mldp ipv4 p2mp
   mdt data 100
  !
 !
!
router pim
 vrf A
  address-family ipv4
   rpf topology route-policy mVPN-Profile-14
   mdt c-multicast-routing bgp
   !
   rp-address 21.21.21.21
   interface GigabitEthernet0/0/0/0
    enable
   !
  !
 !
!
end
```
{% endtab %}

{% tab title="Ingress PE2" %}
```text
hostname PE2
!
vrf A
 vpn id 1:1
 address-family ipv4 unicast
  import route-target
   1:1
  !
  export route-target
   1:1
  !
 !
!
interface Loopback0
 ipv4 address 2.2.2.2 255.255.255.255
!
interface Loopback100
 description VRF-A RP
 vrf A
 ipv4 address 21.21.21.21 255.255.255.255
!
interface GigabitEthernet0/0/0/0
 description to GigabitEthernet0/0.CE2
 vrf A
 ipv4 address 192.168.2.1 255.255.255.0
!
interface GigabitEthernet0/0/0/2
 description to GigabitEthernet0/0/0/2.P
 ipv4 address 10.0.102.2 255.255.255.0
!
route-policy PASS
  pass
end-policy
!
route-policy mVPN-Profile-14
  set core-tree mldp-partitioned-p2mp
end-policy
!
router ospf 1
 router-id 2.2.2.2
 area 0
  mpls ldp auto-config
  interface Loopback0
   passive enable
  !
  interface GigabitEthernet0/0/0/2
   network point-to-point
  !
 !
!
router bgp 1
 bgp router-id 2.2.2.2
 address-family vpnv4 unicast
 !
 address-family ipv4 mvpn
 !
 neighbor 1.1.1.1
  remote-as 1
  update-source Loopback0
  address-family vpnv4 unicast
  !
  address-family ipv4 mvpn
  !
 !
 neighbor 3.3.3.3
  remote-as 1
  update-source Loopback0
  address-family vpnv4 unicast
  !
  address-family ipv4 mvpn
  !
 !
 vrf A
  rd auto
  address-family ipv4 unicast
   redistribute connected
  !
  address-family ipv4 mvpn
  !
  neighbor 192.168.2.2
   remote-as 2
   address-family ipv4 unicast
    route-policy PASS in
    route-policy PASS out
    as-override
   !
  !
 !
!
mpls ldp
 mldp
  address-family ipv4
  !
 !
 router-id 2.2.2.2
!
multicast-routing
 address-family ipv4
  interface Loopback0
   enable
  !
 !
 vrf A
  address-family ipv4
   mdt source Loopback0
   rate-per-route
   interface all enable
   accounting per-prefix
   bgp auto-discovery mldp
   !
   mdt partitioned mldp ipv4 p2mp
   mdt data 100
  !
 !
!
router pim
 vrf A
  address-family ipv4
   rpf topology route-policy mVPN-Profile-14
   mdt c-multicast-routing bgp
   !
   rp-address 21.21.21.21
   interface GigabitEthernet0/0/0/0
    enable
   !
  !
 !
!
end
```
{% endtab %}

{% tab title="Egress PE3" %}
```
hostname PE3
!
vrf A
 vpn id 1:1
 address-family ipv4 unicast
  import route-target
   1:1
  !
  export route-target
   1:1
  !
 !
!
interface Loopback0
 ipv4 address 3.3.3.3 255.255.255.255
!
interface GigabitEthernet0/0/0/0
 description to GigabitEthernet0/0.CE3
 vrf A
 ipv4 address 192.168.3.1 255.255.255.0
!
interface GigabitEthernet0/0/0/3
 description to GigabitEthernet0/0/0/3.P
 ipv4 address 10.0.103.3 255.255.255.0
!
route-policy PASS
  pass
end-policy
!
route-policy mVPN-Profile-14
  set core-tree mldp-partitioned-p2mp
end-policy
!
router ospf 1
 router-id 3.3.3.3
 area 0
  mpls ldp auto-config
  interface Loopback0
   passive enable
  !
  interface GigabitEthernet0/0/0/3
   network point-to-point
  !
 !
!
router bgp 1
 bgp router-id 3.3.3.3
 address-family vpnv4 unicast
 !
 address-family ipv4 mvpn
 !
 neighbor 1.1.1.1
  remote-as 1
  update-source Loopback0
  address-family vpnv4 unicast
  !
  address-family ipv4 mvpn
  !
 !
 neighbor 2.2.2.2
  remote-as 1
  update-source Loopback0
  address-family vpnv4 unicast
  !
  address-family ipv4 mvpn
  !
 !
 vrf A
  rd auto
  address-family ipv4 unicast
   redistribute connected
  !
  address-family ipv4 mvpn
  !
  neighbor 192.168.3.2
   remote-as 2
   address-family ipv4 unicast
    route-policy PASS in
    route-policy PASS out
    as-override
   !
  !
 !
!
mpls ldp
 mldp
  address-family ipv4
  !
 !
 router-id 3.3.3.3
!
multicast-routing
 address-family ipv4
  interface Loopback0
   enable
  !
 !
 vrf A
  address-family ipv4
   mdt source Loopback0
   rate-per-route
   interface all enable
   accounting per-prefix
   bgp auto-discovery mldp
   !
   mdt partitioned mldp ipv4 p2mp
   mdt data 100
  !
 !
!
router pim
 vrf A
  address-family ipv4
   rpf topology route-policy mVPN-Profile-14
   mdt c-multicast-routing bgp
   !
   rp-address 21.21.21.21
   interface GigabitEthernet0/0/0/0
    enable
   !
  !
 !
!
end
```
{% endtab %}

{% tab title="P" %}
```
hostname P
!
interface Loopback0
 ipv4 address 10.10.10.10 255.255.255.255
!
interface GigabitEthernet0/0/0/1
 description to GigabitEthernet0/0/0/1.PE1
 ipv4 address 10.0.101.10 255.255.255.0
!
interface GigabitEthernet0/0/0/2
 description to GigabitEthernet0/0/0/2.PE2
 ipv4 address 10.0.102.10 255.255.255.0
!
interface GigabitEthernet0/0/0/3
 description to GigabitEthernet0/0/0/3.PE3
 ipv4 address 10.0.103.10 255.255.255.0
!
router ospf 1
 router-id 10.10.10.10
 area 0
  mpls ldp auto-config
  interface Loopback0
   passive enable
  !
  interface GigabitEthernet0/0/0/1
   network point-to-point
  !
  interface GigabitEthernet0/0/0/2
   network point-to-point
  !
  interface GigabitEthernet0/0/0/3
   network point-to-point
  !
 !
!
mpls ldp
 mldp
  address-family ipv4
  !
 !
 router-id 10.10.10.10
!
end
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Receiver/CE1" %}
```text
hostname CE1
!
interface Loopback0
 ip address 192.168.0.1 255.255.255.255
 ip pim sparse-mode
 ip igmp join-group 232.1.1.1 source 192.168.2.2
 ip igmp version 3
!
interface Loopback1
 ip address 192.168.101.1 255.255.255.255
 ip pim sparse-mode
 ip igmp join-group 239.1.1.2
!
interface GigabitEthernet0/0
 description to GigabitEthernet0/0/0/0.PE1
 ip address 192.168.1.2 255.255.255.0
 ip pim sparse-mode
!
router bgp 2
 bgp log-neighbor-changes
 neighbor 192.168.1.1 remote-as 1
 !
 address-family ipv4
  redistribute connected
  neighbor 192.168.1.1 activate
 exit-address-family
!
ip pim rp-address 21.21.21.21
ip pim ssm range 1
!
access-list 1 permit 232.0.0.0 0.255.255.255
!
end
```
{% endtab %}

{% tab title="Source/CE2" %}
```
hostname CE2
!
interface GigabitEthernet0/0
 description to GigabitEthernet0/0/0/0.PE2
 ip address 192.168.2.2 255.255.255.0
 ip pim sparse-mode
 duplex auto
 speed auto
 media-type rj45
!
router bgp 2
 bgp log-neighbor-changes
 neighbor 192.168.2.1 remote-as 1
 !
 address-family ipv4
  neighbor 192.168.2.1 activate
 exit-address-family
!
ip pim rp-address 21.21.21.21
!
end
```
{% endtab %}

{% tab title="Receiver/CE3" %}
```
hostname CE3
!
interface Loopback0
 ip address 192.168.0.3 255.255.255.255
 ip pim sparse-mode
 ip igmp join-group 232.1.1.1 source 192.168.2.2
 ip igmp version 3
!
interface Loopback1
 ip address 192.168.101.3 255.255.255.255
 ip pim sparse-mode
 ip igmp join-group 239.1.1.2
!
interface GigabitEthernet0/0
 description to GigabitEthernet0/0/0/0.PE3
 ip address 192.168.3.2 255.255.255.0
 ip pim sparse-mode
!
router bgp 2
 bgp log-neighbor-changes
 neighbor 192.168.3.1 remote-as 1
 !
 address-family ipv4
  redistribute connected
  neighbor 192.168.3.1 activate
 exit-address-family
!
ip pim rp-address 21.21.21.21
ip pim ssm range 1
!
access-list 1 permit 232.0.0.0 0.255.255.255
!
end
```
{% endtab %}
{% endtabs %}

## How to start the multicast streams

### SSM stream

{% tabs %}
{% tab title="CE2" %}
```bash
CE2#ping 232.1.1.1 source 192.168.2.2 repeat 20
Type escape sequence to abort.
Sending 20, 100-byte ICMP Echos to 232.1.1.1, timeout is 2 seconds:
Packet sent with a source address of 192.168.2.2 
.
Reply to request 1 from 192.168.0.1, 52 ms
Reply to request 1 from 192.168.0.3, 52 ms
Reply to request 2 from 192.168.0.1, 12 ms
Reply to request 2 from 192.168.0.3, 12 ms
Reply to request 3 from 192.168.0.3, 12 ms
Reply to request 3 from 192.168.0.1, 13 ms
```
{% endtab %}
{% endtabs %}

### ASM stream

{% tabs %}
{% tab title="CE2" %}
```bash
CE2#ping 239.1.1.2 repeat 3
Type escape sequence to abort.
Sending 3, 100-byte ICMP Echos to 239.1.1.2, timeout is 2 seconds:

Reply to request 0 from 192.168.101.1, 10 ms
Reply to request 0 from 192.168.101.3, 10 ms
Reply to request 1 from 192.168.101.3, 10 ms
Reply to request 1 from 192.168.101.1, 10 ms
Reply to request 2 from 192.168.101.3, 14 ms
Reply to request 2 from 192.168.101.1, 14 ms
```
{% endtab %}
{% endtabs %}

## PE-CE used SSM

### No receivers, no active sources

```text
RP/0/0/CPU0:PE1#sh bgp vrf A ipv4 mvpn 
Fri Jan 29 10:16:00.261 UTC
BGP VRF A, state: Active
BGP Route Distinguisher: 1.1.1.1:0
VRF ID: 0x60000001
BGP router identifier 1.1.1.1, local AS number 1
Non-stop routing is enabled
BGP table state: Active
Table ID: 0x0   RD version: 23
BGP main routing table version 23
BGP NSR Initial initsync version 8 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0

Status codes: s suppressed, d damped, h history, * valid, > best
              i - internal, r RIB-failure, S stale, N Nexthop-discard
Origin codes: i - IGP, e - EGP, ? - incomplete
   Network            Next Hop            Metric LocPrf Weight Path
Route Distinguisher: 1.1.1.1:0 (default for vrf A)
*> [1][1.1.1.1]/40    0.0.0.0                                0 i
*>i[1][2.2.2.2]/40    2.2.2.2                       100      0 i
*>i[1][3.3.3.3]/40    3.3.3.3                       100      0 i
*> [3][0][0.0.0.0][0][0.0.0.0][1.1.1.1]/120
                      0.0.0.0                                0 i
*>i[3][0][0.0.0.0][0][0.0.0.0][2.2.2.2]/120
                      2.2.2.2                       100      0 i
*>i[3][0][0.0.0.0][0][0.0.0.0][3.3.3.3]/120
                      3.3.3.3                       100      0 i

Processed 6 prefixes, 6 paths
```

### Active receiver, no active sources

{% tabs %}
{% tab title="PE1" %}
```text
RP/0/0/CPU0:PE1#sh bgp vrf A ipv4 mvpn 
Fri Jan 29 10:17:27.055 UTC
BGP VRF A, state: Active
BGP Route Distinguisher: 1.1.1.1:0
VRF ID: 0x60000001
BGP router identifier 1.1.1.1, local AS number 1
Non-stop routing is enabled
BGP table state: Active
Table ID: 0x0   RD version: 24
BGP main routing table version 24
BGP NSR Initial initsync version 8 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0

Status codes: s suppressed, d damped, h history, * valid, > best
              i - internal, r RIB-failure, S stale, N Nexthop-discard
Origin codes: i - IGP, e - EGP, ? - incomplete
   Network            Next Hop            Metric LocPrf Weight Path
Route Distinguisher: 1.1.1.1:0 (default for vrf A)
*> [1][1.1.1.1]/40    0.0.0.0                                0 i
*>i[1][2.2.2.2]/40    2.2.2.2                       100      0 i
*>i[1][3.3.3.3]/40    3.3.3.3                       100      0 i
*> [3][0][0.0.0.0][0][0.0.0.0][1.1.1.1]/120
                      0.0.0.0                                0 i
*>i[3][0][0.0.0.0][0][0.0.0.0][2.2.2.2]/120
                      2.2.2.2                       100      0 i
*>i[3][0][0.0.0.0][0][0.0.0.0][3.3.3.3]/120
                      3.3.3.3                       100      0 i
*> [7][2.2.2.2:0][1][32][192.168.2.2][32][232.1.1.1]/184
                      0.0.0.0                                0 i

Processed 7 prefixes, 7 paths
```
{% endtab %}

{% tab title="PE2" %}
```
RP/0/0/CPU0:PE2#sh bgp vrf A ipv4 mvpn 
Fri Jan 29 10:18:35.241 UTC
BGP VRF A, state: Active
BGP Route Distinguisher: 2.2.2.2:0
VRF ID: 0x60000001
BGP router identifier 2.2.2.2, local AS number 1
Non-stop routing is enabled
BGP table state: Active
Table ID: 0x0   RD version: 35
BGP main routing table version 35
BGP NSR Initial initsync version 4 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0

Status codes: s suppressed, d damped, h history, * valid, > best
              i - internal, r RIB-failure, S stale, N Nexthop-discard
Origin codes: i - IGP, e - EGP, ? - incomplete
   Network            Next Hop            Metric LocPrf Weight Path
Route Distinguisher: 2.2.2.2:0 (default for vrf A)
*>i[1][1.1.1.1]/40    1.1.1.1                       100      0 i
*> [1][2.2.2.2]/40    0.0.0.0                                0 i
*>i[1][3.3.3.3]/40    3.3.3.3                       100      0 i
*>i[3][0][0.0.0.0][0][0.0.0.0][1.1.1.1]/120
                      1.1.1.1                       100      0 i
*> [3][0][0.0.0.0][0][0.0.0.0][2.2.2.2]/120
                      0.0.0.0                                0 i
*>i[3][0][0.0.0.0][0][0.0.0.0][3.3.3.3]/120
                      3.3.3.3                       100      0 i
*>i[7][2.2.2.2:0][1][32][192.168.2.2][32][232.1.1.1]/184
                      1.1.1.1                       100      0 i
* i                   3.3.3.3                       100      0 i

Processed 7 prefixes, 8 paths
```
{% endtab %}

{% tab title="PE3" %}
```
RP/0/0/CPU0:PE3#sh bgp vrf A ipv4 mvpn 
Fri Jan 29 10:19:00.929 UTC
BGP VRF A, state: Active
BGP Route Distinguisher: 3.3.3.3:0
VRF ID: 0x60000001
BGP router identifier 3.3.3.3, local AS number 1
Non-stop routing is enabled
BGP table state: Active
Table ID: 0x0   RD version: 24
BGP main routing table version 24
BGP NSR Initial initsync version 4 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0

Status codes: s suppressed, d damped, h history, * valid, > best
              i - internal, r RIB-failure, S stale, N Nexthop-discard
Origin codes: i - IGP, e - EGP, ? - incomplete
   Network            Next Hop            Metric LocPrf Weight Path
Route Distinguisher: 3.3.3.3:0 (default for vrf A)
*>i[1][1.1.1.1]/40    1.1.1.1                       100      0 i
*>i[1][2.2.2.2]/40    2.2.2.2                       100      0 i
*> [1][3.3.3.3]/40    0.0.0.0                                0 i
*>i[3][0][0.0.0.0][0][0.0.0.0][1.1.1.1]/120
                      1.1.1.1                       100      0 i
*>i[3][0][0.0.0.0][0][0.0.0.0][2.2.2.2]/120
                      2.2.2.2                       100      0 i
*> [3][0][0.0.0.0][0][0.0.0.0][3.3.3.3]/120
                      0.0.0.0                                0 i
*> [7][2.2.2.2:0][1][32][192.168.2.2][32][232.1.1.1]/184
                      0.0.0.0                                0 i

Processed 7 prefixes, 7 paths
```
{% endtab %}
{% endtabs %}

### No receivers, there's an active source

```text

```

### Active receivers and active source

```text

```

## PE-CE uses ASM

### No receivers, no active sources

```text

```

### Active receiver, no active sources

```text

```

### No receivers, there's an active source

```text

```

## mVPN interoperability issues

Several vendors depending on a software version are using pre-RFC encoding for `C-Mcast Import RT`. In this case you won't find a correct extended community in VPNv4 updates. Instead there will be \_\_\_\_\_\_\_.

#### Wrong encoding

Below you can find CLI to switch to RFC encoding.

{% tabs %}
{% tab title="Nokia" %}
```text

```
{% endtab %}

{% tab title="Juniper" %}
```

```
{% endtab %}
{% endtabs %}




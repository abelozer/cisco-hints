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

{% hint style="info" %}
RP configuration is required for ASM case only.
{% endhint %}

{% tabs %}
{% tab title="Egress PE1" %}
```
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
```
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
```
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
```applescript
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
```applescript
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

In short it means that first hop routers (CE1 and CE3) receive IGMPv3 join where both source address and multicast group are specified. mVPN involves routes Type 1, 3 and 7 in this case.



**Route types**

| Type   | Name                      | Type        | Definition |
| ------ | ------------------------- | ----------- | ---------- |
| Type 1 | Intra-AS I-PMSI A-D route | AD          |            |
| Type 2 | Inter-AS I-PMSI A-D route | AD          |            |
| Type 3 | S-PMSI A-D route          | AD          |            |
| Type 4 | Leaf A-D route            | AD          |            |
| Type 5 | Source Active A-D route   | AD          |            |
| Type 6 | Shared Tree Join route    | C-multicast |            |
| Type 7 | Source Tree Join route    | C-multicast |            |

### 1. No receivers, no active sources

All PEs are announcing Type 1 and Type 3.

```applescript
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

```applescript
RP/0/0/CPU0:PE1#sh bgp vrf A ipv4 mvpn [1][3.3.3.3]/40 detail 
Mon Feb  1 08:19:15.073 UTC
BGP routing table entry for [1][3.3.3.3]/40, Route Distinguisher: 1.1.1.1:0
Versions:
  Process           bRIB/RIB  SendTblVer
  Speaker                 14          14
    Flags: 0x00001001+0x00000000; 
Last Modified: Jan 29 20:33:50.450 for 2d11h
Paths: (1 available, best #1, not advertised to EBGP peer)
  Not advertised to any peer
  Path #1: Received by speaker 0
  Flags: 0x4000000005060005, import: 0x80
  Not advertised to any peer
  Local
    3.3.3.3 (metric 3) from 3.3.3.3 (3.3.3.3)
      Origin IGP, localpref 100, valid, internal, best, group-best, import-candidate, imported
      Received Path ID 0, Local Path ID 0, version 14
      Community: no-export
      Extended community: RT:1:1 
      Source AFI: IPv4 MVPN, Source VRF: default, Source Route Distinguisher: 3.3.3.3:0
```

```applescript
RP/0/0/CPU0:PE1#sh bgp vrf A ipv4 mvpn [3][0][0.0.0.0][0][0.0.0.0][2.2.2.2]/12$
Mon Feb  1 08:20:26.588 UTC
BGP routing table entry for [3][0][0.0.0.0][0][0.0.0.0][2.2.2.2]/120, Route Distinguisher: 1.1.1.1:0
Versions:
  Process           bRIB/RIB  SendTblVer
  Speaker                  8           8
    Flags: 0x00001001+0x00000000; 
Last Modified: Jan 29 20:33:50.450 for 2d11h
Paths: (1 available, best #1, not advertised to EBGP peer)
  Not advertised to any peer
  Path #1: Received by speaker 0
  Flags: 0x4000000005060005, import: 0x80
  Not advertised to any peer
  Local
    2.2.2.2 (metric 3) from 2.2.2.2 (2.2.2.2)
      Origin IGP, localpref 100, valid, internal, best, group-best, import-candidate, imported
      Received Path ID 0, Local Path ID 0, version 8
      Community: no-export
      Extended community: RT:1:1 
      PMSI: flags 0x00, type 2, label 0, ID 0x0600010402020202000701000400000001
      PPMP: label 24000
      Source AFI: IPv4 MVPN, Source VRF: default, Source Route Distinguisher: 2.2.2.2:0
```

### 2. Active receiver, no active sources

When egress PEs receive PIM Join  (C-S, C-G) they generate Type 7 to express the presence of receiver behind. As you can see from PE2 output (ingress PE) it gets Type 7 routes from both egress PEs.

#### IGMP state on CE routers

{% tabs %}
{% tab title="CE1" %}
```applescript
CE1#sh ip igmp membership 232.1.1.1
Flags: A  - aggregate, T - tracked
       L  - Local, S - static, V - virtual, R - Reported through v3 
       I - v3lite, U - Urd, M - SSM (S,G) channel 
       1,2,3 - The version of IGMP, the group is in
Channel/Group-Flags: 
       / - Filtering entry (Exclude mode (S,G), Include mode (G))
Reporter:
       <mac-or-ip-address> - last reporter if group is not explicitly tracked
       <n>/<m>      - <n> reporter in include mode, <m> reporter in exclude

 Channel/Group                  Reporter        Uptime   Exp.  Flags  Interface 
/*,232.1.1.1                    192.168.0.1     04:57:52 stop  3LMA   Lo0
 192.168.2.2,232.1.1.1                          04:57:52 02:57 A      Lo0
```
{% endtab %}

{% tab title="CE3" %}
```applescript
CE3#sh ip igmp membership 232.1.1.1
Flags: A  - aggregate, T - tracked
       L  - Local, S - static, V - virtual, R - Reported through v3 
       I - v3lite, U - Urd, M - SSM (S,G) channel 
       1,2,3 - The version of IGMP, the group is in
Channel/Group-Flags: 
       / - Filtering entry (Exclude mode (S,G), Include mode (G))
Reporter:
       <mac-or-ip-address> - last reporter if group is not explicitly tracked
       <n>/<m>      - <n> reporter in include mode, <m> reporter in exclude

 Channel/Group                  Reporter        Uptime   Exp.  Flags  Interface 
/*,232.1.1.1                    192.168.0.3     04:58:29 stop  3LMA   Lo0
 192.168.2.2,232.1.1.1                          04:58:29 02:16 A      Lo0
```
{% endtab %}
{% endtabs %}

#### PIM state on PE routers

PE1 and PE3 have received PIM Joins from respective CE routers.

{% tabs %}
{% tab title="PE1" %}
```applescript
RP/0/0/CPU0:PE1#sh pim vrf A topology 232.1.1.1
Fri Jan 29 13:55:41.748 UTC

IP PIM Multicast Topology Table
Entry state: (*/S,G)[RPT/SPT] Protocol Uptime Info
Entry flags: KAT - Keep Alive Timer, AA - Assume Alive, PA - Probe Alive
    RA - Really Alive, IA - Inherit Alive, LH - Last Hop
    DSS - Don't Signal Sources,  RR - Register Received
    SR - Sending Registers, SNR - Sending Null Registers
    E - MSDP External, EX - Extranet
    MFA - Mofrr Active, MFP - Mofrr Primary, MFB - Mofrr Backup
    DCC - Don't Check Connected, ME - MDT Encap, MD - MDT Decap
    MT - Crossed Data MDT threshold, MA - Data MDT Assigned
    SAJ - BGP Source Active Joined, SAR - BGP Source Active Received,
    SAS - BGP Source Active Sent, IM - Inband mLDP, X - VxLAN
Interface state: Name, Uptime, Fwd, Info
Interface flags: LI - Local Interest, LD - Local Dissinterest,
    II - Internal Interest, ID - Internal Dissinterest,
    LH - Last Hop, AS - Assert, AB - Admin Boundary, EX - Extranet,
    BGP - BGP C-Multicast Join, BP - BGP Source Active Prune,
    MVS - MVPN Safi Learned, MV6S - MVPN IPv6 Safi Learned

(192.168.2.2,232.1.1.1)SPT SSM Up: 00:16:32 
JP: Join(BGP) RPF: LmdtA,2.2.2.2 Flags: 
  GigabitEthernet0/0/0/0      00:16:32  fwd Join(00:02:55)
```
{% endtab %}

{% tab title="PE2" %}
```applescript
RP/0/0/CPU0:PE2#sh pim vrf A topology 232.1.1.1
Fri Jan 29 13:56:09.387 UTC

IP PIM Multicast Topology Table
Entry state: (*/S,G)[RPT/SPT] Protocol Uptime Info
Entry flags: KAT - Keep Alive Timer, AA - Assume Alive, PA - Probe Alive
    RA - Really Alive, IA - Inherit Alive, LH - Last Hop
    DSS - Don't Signal Sources,  RR - Register Received
    SR - Sending Registers, SNR - Sending Null Registers
    E - MSDP External, EX - Extranet
    MFA - Mofrr Active, MFP - Mofrr Primary, MFB - Mofrr Backup
    DCC - Don't Check Connected, ME - MDT Encap, MD - MDT Decap
    MT - Crossed Data MDT threshold, MA - Data MDT Assigned
    SAJ - BGP Source Active Joined, SAR - BGP Source Active Received,
    SAS - BGP Source Active Sent, IM - Inband mLDP, X - VxLAN
Interface state: Name, Uptime, Fwd, Info
Interface flags: LI - Local Interest, LD - Local Dissinterest,
    II - Internal Interest, ID - Internal Dissinterest,
    LH - Last Hop, AS - Assert, AB - Admin Boundary, EX - Extranet,
    BGP - BGP C-Multicast Join, BP - BGP Source Active Prune,
    MVS - MVPN Safi Learned, MV6S - MVPN IPv6 Safi Learned

(192.168.2.2,232.1.1.1)SPT SSM Up: 00:17:01 
JP: Join(now) RPF: GigabitEthernet0/0/0/0,192.168.2.2* Flags: 
  LmdtA                       00:17:01  fwd BGP
```
{% endtab %}

{% tab title="PE3" %}
```applescript
RP/0/0/CPU0:PE3#sh pim vrf A topology 232.1.1.1
Fri Jan 29 13:56:43.895 UTC

IP PIM Multicast Topology Table
Entry state: (*/S,G)[RPT/SPT] Protocol Uptime Info
Entry flags: KAT - Keep Alive Timer, AA - Assume Alive, PA - Probe Alive
    RA - Really Alive, IA - Inherit Alive, LH - Last Hop
    DSS - Don't Signal Sources,  RR - Register Received
    SR - Sending Registers, SNR - Sending Null Registers
    E - MSDP External, EX - Extranet
    MFA - Mofrr Active, MFP - Mofrr Primary, MFB - Mofrr Backup
    DCC - Don't Check Connected, ME - MDT Encap, MD - MDT Decap
    MT - Crossed Data MDT threshold, MA - Data MDT Assigned
    SAJ - BGP Source Active Joined, SAR - BGP Source Active Received,
    SAS - BGP Source Active Sent, IM - Inband mLDP, X - VxLAN
Interface state: Name, Uptime, Fwd, Info
Interface flags: LI - Local Interest, LD - Local Dissinterest,
    II - Internal Interest, ID - Internal Dissinterest,
    LH - Last Hop, AS - Assert, AB - Admin Boundary, EX - Extranet,
    BGP - BGP C-Multicast Join, BP - BGP Source Active Prune,
    MVS - MVPN Safi Learned, MV6S - MVPN IPv6 Safi Learned

(192.168.2.2,232.1.1.1)SPT SSM Up: 00:17:28 
JP: Join(BGP) RPF: LmdtA,2.2.2.2 Flags: 
  GigabitEthernet0/0/0/0      00:17:28  fwd Join(00:03:07)
```
{% endtab %}
{% endtabs %}

#### MRIB state on PE routers

{% tabs %}
{% tab title="PE1" %}
```
RP/0/0/CPU0:PE1#sh mrib vrf A route 232.1.1.1
Fri Jan 29 13:40:47.140 UTC

IP Multicast Routing Information Base
Entry flags: L - Domain-Local Source, E - External Source to the Domain,
    C - Directly-Connected Check, S - Signal, IA - Inherit Accept,
    IF - Inherit From, D - Drop, ME - MDT Encap, EID - Encap ID,
    MD - MDT Decap, MT - MDT Threshold Crossed, MH - MDT interface handle
    CD - Conditional Decap, MPLS - MPLS Decap, EX - Extranet
    MoFE - MoFRR Enabled, MoFS - MoFRR State, MoFP - MoFRR Primary
    MoFB - MoFRR Backup, RPFID - RPF ID Set, X - VXLAN
Interface flags: F - Forward, A - Accept, IC - Internal Copy,
    NS - Negate Signal, DP - Don't Preserve, SP - Signal Present,
    II - Internal Interest, ID - Internal Disinterest, LI - Local Interest,
    LD - Local Disinterest, DI - Decapsulation Interface
    EI - Encapsulation Interface, MI - MDT Interface, LVIF - MPLS Encap,
    EX - Extranet, A2 - Secondary Accept, MT - MDT Threshold Crossed,
    MA - Data MDT Assigned, LMI - mLDP MDT Interface, TMI - P2MP-TE MDT Interface
    IRMI - IR MDT Interface

(192.168.2.2,232.1.1.1) RPF nbr: 2.2.2.2 Flags: RPF
  Up: 00:01:38
  Incoming Interface List
    LmdtA Flags: A LMI, Up: 00:01:38
  Outgoing Interface List
    GigabitEthernet0/0/0/0 Flags: F NS, Up: 00:01:38
```
{% endtab %}

{% tab title="PE2" %}
```
RP/0/0/CPU0:PE2#sh mri vrf A route 232.1.1.1
Fri Jan 29 13:40:00.343 UTC

IP Multicast Routing Information Base
Entry flags: L - Domain-Local Source, E - External Source to the Domain,
    C - Directly-Connected Check, S - Signal, IA - Inherit Accept,
    IF - Inherit From, D - Drop, ME - MDT Encap, EID - Encap ID,
    MD - MDT Decap, MT - MDT Threshold Crossed, MH - MDT interface handle
    CD - Conditional Decap, MPLS - MPLS Decap, EX - Extranet
    MoFE - MoFRR Enabled, MoFS - MoFRR State, MoFP - MoFRR Primary
    MoFB - MoFRR Backup, RPFID - RPF ID Set, X - VXLAN
Interface flags: F - Forward, A - Accept, IC - Internal Copy,
    NS - Negate Signal, DP - Don't Preserve, SP - Signal Present,
    II - Internal Interest, ID - Internal Disinterest, LI - Local Interest,
    LD - Local Disinterest, DI - Decapsulation Interface
    EI - Encapsulation Interface, MI - MDT Interface, LVIF - MPLS Encap,
    EX - Extranet, A2 - Secondary Accept, MT - MDT Threshold Crossed,
    MA - Data MDT Assigned, LMI - mLDP MDT Interface, TMI - P2MP-TE MDT Interface
    IRMI - IR MDT Interface

(192.168.2.2,232.1.1.1) RPF nbr: 192.168.2.2 Flags: RPF
  Up: 00:00:52
  Incoming Interface List
    GigabitEthernet0/0/0/0 Flags: A, Up: 00:00:52
  Outgoing Interface List
    LmdtA Flags: F LMI TR, Up: 00:00:52
```
{% endtab %}

{% tab title="PE3" %}
```
RP/0/0/CPU0:PE3#sh mrib vrf A route 232.1.1.1
Fri Jan 29 13:43:01.961 UTC

IP Multicast Routing Information Base
Entry flags: L - Domain-Local Source, E - External Source to the Domain,
    C - Directly-Connected Check, S - Signal, IA - Inherit Accept,
    IF - Inherit From, D - Drop, ME - MDT Encap, EID - Encap ID,
    MD - MDT Decap, MT - MDT Threshold Crossed, MH - MDT interface handle
    CD - Conditional Decap, MPLS - MPLS Decap, EX - Extranet
    MoFE - MoFRR Enabled, MoFS - MoFRR State, MoFP - MoFRR Primary
    MoFB - MoFRR Backup, RPFID - RPF ID Set, X - VXLAN
Interface flags: F - Forward, A - Accept, IC - Internal Copy,
    NS - Negate Signal, DP - Don't Preserve, SP - Signal Present,
    II - Internal Interest, ID - Internal Disinterest, LI - Local Interest,
    LD - Local Disinterest, DI - Decapsulation Interface
    EI - Encapsulation Interface, MI - MDT Interface, LVIF - MPLS Encap,
    EX - Extranet, A2 - Secondary Accept, MT - MDT Threshold Crossed,
    MA - Data MDT Assigned, LMI - mLDP MDT Interface, TMI - P2MP-TE MDT Interface
    IRMI - IR MDT Interface

(192.168.2.2,232.1.1.1) RPF nbr: 2.2.2.2 Flags: RPF
  Up: 00:03:46
  Incoming Interface List
    LmdtA Flags: A LMI, Up: 00:03:46
  Outgoing Interface List
    GigabitEthernet0/0/0/0 Flags: F NS, Up: 00:03:46
```
{% endtab %}
{% endtabs %}

#### BGP state

{% tabs %}
{% tab title="PE1" %}
```
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

#### Type 7 in more details

```
RP/0/0/CPU0:PE1#sh bgp vrf A ipv4 mvpn [7][2.2.2.2:0][1][32][192.168.2.2][32][$
Fri Jan 29 14:49:13.348 UTC
BGP routing table entry for [7][2.2.2.2:0][1][32][192.168.2.2][32][232.1.1.1]/184, Route Distinguisher: 1.1.1.1:0
Versions:
  Process           bRIB/RIB  SendTblVer
  Speaker                 26          26
Last Modified: Jan 29 13:39:08.875 for 01:10:04
Paths: (1 available, best #1)
  Advertised to PE update-groups (with more than one peer):
    0.2 
  Path #1: Received by speaker 0
  Advertised to PE update-groups (with more than one peer):
    0.2 
  Local
    0.0.0.0 from 0.0.0.0 (1.1.1.1)
      Origin IGP, localpref 100, valid, redistributed, best, group-best, import-candidate
      Received Path ID 0, Local Path ID 0, version 26
      Extended community: RT:2.2.2.2:16
```

Let's look at IPv4 unicast route:. There's an extended community called `VRF Route Import` with the value `2.2.2.2:16`.

```
RP/0/0/CPU0:PE1#sh bgp vrf A ipv4 unicast 192.168.2.0/24
Fri Jan 29 14:50:55.851 UTC
BGP routing table entry for 192.168.2.0/24, Route Distinguisher: 1.1.1.1:0
Versions:
  Process           bRIB/RIB  SendTblVer
  Speaker                 24          24
Last Modified: Jan 29 09:47:07.875 for 05:03:48
Paths: (1 available, best #1)
  Advertised to CE peers (in unique update groups):
    192.168.1.2     
  Path #1: Received by speaker 0
  Advertised to CE peers (in unique update groups):
    192.168.1.2     
  Local
    2.2.2.2 (metric 3) from 2.2.2.2 (2.2.2.2)
      Received Label 24006 
      Origin incomplete, metric 0, localpref 100, valid, internal, best, group-best, import-candidate, imported
      Received Path ID 0, Local Path ID 0, version 24
      Extended community: VRF Route Import:2.2.2.2:16 Source AS:1:0 RT:1:1 
      Connector: type: 1, Value:2.2.2.2:0:2.2.2.2
      Source AFI: VPNv4 Unicast, Source VRF: default, Source Route Distinguisher: 2.2.2.2:0
```

#### MLDP

{% tabs %}
{% tab title="PE1" %}
```
RP/0/0/CPU0:PE1#sh mpl mldp database brief   
Fri Jan 29 20:39:59.845 UTC

LSM ID   Type    Root              Up Down Decoded Opaque Value
0x00001  P2MP    1.1.1.1           0  1    [global-id 1]                 
0x00002  P2MP    2.2.2.2           1  1    [global-id 1]


RP/0/0/CPU0:PE1#sh mpl mldp database       
Fri Jan 29 20:40:45.081 UTC
mLDP database
LSM-ID: 0x00001  Type: P2MP  Uptime: 00:08:34
  FEC Root           : 1.1.1.1 (we are the root)
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    PIM MDT            Uptime: 00:08:34
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)

LSM-ID: 0x00002  Type: P2MP  Uptime: 00:06:54
  FEC Root           : 2.2.2.2 
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    10.10.10.10:0 [Active] Uptime: 00:06:54  
      Local Label (D) : 24006             
  Downstream  client(s): 
    PIM MDT            Uptime: 00:06:54
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      RPF ID          : 3
      RD              : 514:33685504


RP/0/0/CPU0:PE1#sh mpl mldp database details 
Fri Jan 29 20:41:41.848 UTC
mLDP database
LSM-ID: 0x00001  Type: P2MP  Uptime: 00:09:31
  FEC Root           : 1.1.1.1 (we are the root)
  FEC Length         : 12 bytes
  FEC Value internal : 020100040000000101010101
  Opaque length      : 4 bytes
  Opaque value       : 01 0004 00000001
  Opaque decoded     : [global-id 1]
  Features           : Trace
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    PIM MDT            Uptime: 00:09:31
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      Peek            : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)

LSM-ID: 0x00002  Type: P2MP  Uptime: 00:07:51
  FEC Root           : 2.2.2.2 
  FEC Length         : 12 bytes
  FEC Value internal : 020100040000000102020202
  Opaque length      : 4 bytes
  Opaque value       : 01 0004 00000001
  Opaque decoded     : [global-id 1]
  Features           : Trace
  Upstream neighbor(s) :
    10.10.10.10:0 [Active] Uptime: 00:07:51  
      Local Label (D) : 24006             
  Downstream  client(s): 
    PIM MDT            Uptime: 00:07:51
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      RPF ID          : 3
      Peek            : Yes
      RD              : 514:33685504
```
{% endtab %}

{% tab title="PE2" %}
```
RP/0/0/CPU0:PE2#sh mpl mldp database brief
Fri Jan 29 20:42:44.193 UTC

LSM ID   Type    Root              Up Down Decoded Opaque Value
0x00001  P2MP    2.2.2.2           0  2    [global-id 1]                 


RP/0/0/CPU0:PE2#sh mpl mldp database      
Fri Jan 29 20:42:52.213 UTC
mLDP database
LSM-ID: 0x00001  Type: P2MP  Uptime: 00:12:40
  FEC Root           : 2.2.2.2 (we are the root)
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    LDP 10.10.10.10:0  Uptime: 00:09:02
      Next Hop         : 10.0.102.10
      Interface        : GigabitEthernet0/0/0/2 
      Remote label (D) : 24003             
    PIM MDT            Uptime: 00:12:40
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)


RP/0/0/CPU0:PE2#sh mpl mldp database details 
Fri Jan 29 20:42:59.702 UTC
mLDP database
LSM-ID: 0x00001  Type: P2MP  Uptime: 00:12:48
  FEC Root           : 2.2.2.2 (we are the root)
  FEC Length         : 12 bytes
  FEC Value internal : 020100040000000102020202
  Opaque length      : 4 bytes
  Opaque value       : 01 0004 00000001
  Opaque decoded     : [global-id 1]
  Features           : Trace
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    LDP 10.10.10.10:0  Uptime: 00:09:09
      Next Hop         : 10.0.102.10
      Interface        : GigabitEthernet0/0/0/2 
      Remote label (D) : 24003             
      LDP MSG ID       : 35
    PIM MDT            Uptime: 00:12:48
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      Peek            : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)
```
{% endtab %}

{% tab title="PE3" %}
```
RP/0/0/CPU0:PE3#sh mpl mldp database brief
Fri Jan 29 20:51:16.139 UTC

LSM ID   Type    Root              Up Down Decoded Opaque Value
0x00002  P2MP    2.2.2.2           1  1    [global-id 1]                 
0x00001  P2MP    3.3.3.3           0  1    [global-id 1]

                              
RP/0/0/CPU0:PE3#sh mpl mldp database      
Fri Jan 29 20:51:18.288 UTC
mLDP database
LSM-ID: 0x00002  Type: P2MP  Uptime: 00:17:28
  FEC Root           : 2.2.2.2 
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    10.10.10.10:0 [Active] Uptime: 00:17:28  
      Local Label (D) : 24006             
  Downstream  client(s): 
    PIM MDT            Uptime: 00:17:28
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      RPF ID          : 3
      RD              : 514:33685504

LSM-ID: 0x00001  Type: P2MP  Uptime: 00:19:10
  FEC Root           : 3.3.3.3 (we are the root)
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    PIM MDT            Uptime: 00:19:10
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)


RP/0/0/CPU0:PE3#sh mpl mldp database details 
Fri Jan 29 20:51:22.808 UTC
mLDP database
LSM-ID: 0x00002  Type: P2MP  Uptime: 00:17:32
  FEC Root           : 2.2.2.2 
  FEC Length         : 12 bytes
  FEC Value internal : 020100040000000102020202
  Opaque length      : 4 bytes
  Opaque value       : 01 0004 00000001
  Opaque decoded     : [global-id 1]
  Features           : Trace
  Upstream neighbor(s) :
    10.10.10.10:0 [Active] Uptime: 00:17:32  
      Local Label (D) : 24006             
  Downstream  client(s): 
    PIM MDT            Uptime: 00:17:32
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      RPF ID          : 3
      Peek            : Yes
      RD              : 514:33685504

LSM-ID: 0x00001  Type: P2MP  Uptime: 00:19:15
  FEC Root           : 3.3.3.3 (we are the root)
  FEC Length         : 12 bytes
  FEC Value internal : 020100040000000103030303
  Opaque length      : 4 bytes
  Opaque value       : 01 0004 00000001
  Opaque decoded     : [global-id 1]
  Features           : Trace
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    PIM MDT            Uptime: 00:19:15
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      Peek            : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)
```
{% endtab %}

{% tab title="P" %}
```
RP/0/0/CPU0:P#sh mpl mldp database brief
Fri Jan 29 21:15:57.137 UTC

LSM ID   Type    Root              Up Down Decoded Opaque Value
0x00001  P2MP    2.2.2.2           1  2    [global-id 1]                 


RP/0/0/CPU0:P#sh mpl mldp database      
Fri Jan 29 21:15:59.376 UTC
mLDP database
LSM-ID: 0x00001  Type: P2MP  Uptime: 00:42:08
  FEC Root           : 2.2.2.2 
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    2.2.2.2:0 [Active] Uptime: 00:42:08  
      Local Label (D) : 24003             
  Downstream  client(s): 
    LDP 1.1.1.1:0      Uptime: 00:42:08
      Next Hop         : 10.0.101.1
      Interface        : GigabitEthernet0/0/0/1 
      Remote label (D) : 24006             
    LDP 3.3.3.3:0      Uptime: 00:42:08
      Next Hop         : 10.0.103.3
      Interface        : GigabitEthernet0/0/0/3 
      Remote label (D) : 24006             


RP/0/0/CPU0:P#sh mpl mldp database details 
Fri Jan 29 21:16:02.986 UTC
mLDP database
LSM-ID: 0x00001  Type: P2MP  Uptime: 00:42:12
  FEC Root           : 2.2.2.2 
  FEC Length         : 12 bytes
  FEC Value internal : 020100040000000102020202
  Opaque length      : 4 bytes
  Opaque value       : 01 0004 00000001
  Opaque decoded     : [global-id 1]
  Features           : Trace
  Upstream neighbor(s) :
    2.2.2.2:0 [Active] Uptime: 00:42:12  
      Local Label (D) : 24003             
  Downstream  client(s): 
    LDP 1.1.1.1:0      Uptime: 00:42:12
      Next Hop         : 10.0.101.1
      Interface        : GigabitEthernet0/0/0/1 
      Remote label (D) : 24006             
      LDP MSG ID       : 12
    LDP 3.3.3.3:0      Uptime: 00:42:12
      Next Hop         : 10.0.103.3
      Interface        : GigabitEthernet0/0/0/3 
      Remote label (D) : 24006             
      LDP MSG ID       : 5
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="PE1" %}
```
RP/0/0/CPU0:PE1#show mpls mldp bindings 
Fri Jan 29 20:49:35.925 UTC
mLDP MPLS Bindings database

LSP-ID: 0x00001 Paths: 1 Flags: Pk
 0x00001 P2MP   1.1.1.1 [global-id 1]
   Local Label: 24000 Remote: 1048577 Inft: LmdtA RPF-ID: 0 TIDv4/v6: 0xE0000010/0xE0800010

LSP-ID: 0x00002 Paths: 2 Flags: Pk
 0x00002 P2MP   2.2.2.2 [global-id 1]
   Local Label: 24006 Active
   Remote Label: 1048577 Inft: LmdtA RPF-ID: 3 TIDv4/v6: 0xE0000010/0xE0800010
```
{% endtab %}

{% tab title="PE2" %}
```
RP/0/0/CPU0:PE2#show mpls mldp bindings
Fri Jan 29 20:50:08.693 UTC
mLDP MPLS Bindings database

LSP-ID: 0x00001 Paths: 2 Flags: Pk
 0x00001 P2MP   2.2.2.2 [global-id 1]
   Local Label: 24000 Remote: 1048577 Inft: LmdtA RPF-ID: 0 TIDv4/v6: 0xE0000010/0xE0800010
   Remote Label: 24003 NH: 10.0.102.10 Inft: GigabitEthernet0/0/0/2
```
{% endtab %}

{% tab title="PE3" %}
```
RP/0/0/CPU0:PE3#show mpls mldp bindings
Fri Jan 29 20:50:42.431 UTC
mLDP MPLS Bindings database

LSP-ID: 0x00001 Paths: 1 Flags: Pk
 0x00001 P2MP   3.3.3.3 [global-id 1]
   Local Label: 24000 Remote: 1048577 Inft: LmdtA RPF-ID: 0 TIDv4/v6: 0xE0000010/0xE0800010

LSP-ID: 0x00002 Paths: 2 Flags: Pk
 0x00002 P2MP   2.2.2.2 [global-id 1]
   Local Label: 24006 Active
   Remote Label: 1048577 Inft: LmdtA RPF-ID: 3 TIDv4/v6: 0xE0000010/0xE0800010
```
{% endtab %}

{% tab title="P" %}
```
RP/0/0/CPU0:P#show mpls mldp bindings
Fri Jan 29 21:15:17.199 UTC
mLDP MPLS Bindings database

LSP-ID: 0x00001 Paths: 3 Flags:
 0x00001 P2MP   2.2.2.2 [global-id 1]
   Local Label: 24003 Active
   Remote Label: 24006 NH: 10.0.101.1 Inft: GigabitEthernet0/0/0/1
   Remote Label: 24006 NH: 10.0.103.3 Inft: GigabitEthernet0/0/0/3
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="PE1" %}
```
RP/0/0/CPU0:PE1#show mpls mldp lsm-id
Fri Jan 29 20:52:13.344 UTC
mLDP database
LSM-ID: 0x00001  VRF: default  Type: P2MP  Uptime: 00:20:02
  FEC Root           : 1.1.1.1 (we are the root)
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    PIM MDT            Uptime: 00:20:02
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)

LSM-ID: 0x00002  VRF: default  Type: P2MP  Uptime: 00:18:22
  FEC Root           : 2.2.2.2 
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    10.10.10.10:0 [Active] Uptime: 00:18:22  
      Local Label (D) : 24006             
  Downstream  client(s): 
    PIM MDT            Uptime: 00:18:22
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      RPF ID          : 3
      RD              : 514:33685504
```
{% endtab %}

{% tab title="PE2" %}
```
RP/0/0/CPU0:PE2#show mpls mldp lsm-id
Fri Jan 29 20:53:00.801 UTC
mLDP database
LSM-ID: 0x00001  VRF: default  Type: P2MP  Uptime: 00:22:49
  FEC Root           : 2.2.2.2 (we are the root)
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    LDP 10.10.10.10:0  Uptime: 00:19:10
      Next Hop         : 10.0.102.10
      Interface        : GigabitEthernet0/0/0/2 
      Remote label (D) : 24003             
    PIM MDT            Uptime: 00:22:49
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)
```
{% endtab %}

{% tab title="PE3" %}
```
RP/0/0/CPU0:PE3#show mpls mldp lsm-id
Fri Jan 29 20:53:35.669 UTC
mLDP database
LSM-ID: 0x00001  VRF: default  Type: P2MP  Uptime: 00:21:28
  FEC Root           : 3.3.3.3 (we are the root)
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    PIM MDT            Uptime: 00:21:28
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)

LSM-ID: 0x00002  VRF: default  Type: P2MP  Uptime: 00:19:45
  FEC Root           : 2.2.2.2 
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    10.10.10.10:0 [Active] Uptime: 00:19:45  
      Local Label (D) : 24006             
  Downstream  client(s): 
    PIM MDT            Uptime: 00:19:45
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      RPF ID          : 3
      RD              : 514:33685504
```
{% endtab %}

{% tab title="P" %}
```
RP/0/0/CPU0:P#show mpls mldp lsm-id
Fri Jan 29 21:14:50.611 UTC
mLDP database
LSM-ID: 0x00001  VRF: default  Type: P2MP  Uptime: 00:41:00
  FEC Root           : 2.2.2.2 
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    2.2.2.2:0 [Active] Uptime: 00:41:00  
      Local Label (D) : 24003             
  Downstream  client(s): 
    LDP 1.1.1.1:0      Uptime: 00:41:00
      Next Hop         : 10.0.101.1
      Interface        : GigabitEthernet0/0/0/1 
      Remote label (D) : 24006             
    LDP 3.3.3.3:0      Uptime: 00:41:00
      Next Hop         : 10.0.103.3
      Interface        : GigabitEthernet0/0/0/3 
      Remote label (D) : 24006
```
{% endtab %}
{% endtabs %}

```
RP/0/0/CPU0:PE2#sh mrib vrf A route 232.1.1.1 192.168.2.2 detail 
Fri Jan 29 21:08:04.149 UTC

IP Multicast Routing Information Base
Entry flags: L - Domain-Local Source, E - External Source to the Domain,
    C - Directly-Connected Check, S - Signal, IA - Inherit Accept,
    IF - Inherit From, D - Drop, ME - MDT Encap, EID - Encap ID,
    MD - MDT Decap, MT - MDT Threshold Crossed, MH - MDT interface handle
    CD - Conditional Decap, MPLS - MPLS Decap, EX - Extranet
    MoFE - MoFRR Enabled, MoFS - MoFRR State, MoFP - MoFRR Primary
    MoFB - MoFRR Backup, RPFID - RPF ID Set, X - VXLAN
Interface flags: F - Forward, A - Accept, IC - Internal Copy,
    NS - Negate Signal, DP - Don't Preserve, SP - Signal Present,
    II - Internal Interest, ID - Internal Disinterest, LI - Local Interest,
    LD - Local Disinterest, DI - Decapsulation Interface
    EI - Encapsulation Interface, MI - MDT Interface, LVIF - MPLS Encap,
    EX - Extranet, A2 - Secondary Accept, MT - MDT Threshold Crossed,
    MA - Data MDT Assigned, LMI - mLDP MDT Interface, TMI - P2MP-TE MDT Interface
    IRMI - IR MDT Interface

(192.168.2.2,232.1.1.1) Ver: 0x611e RPF nbr: 192.168.2.2 Flags: RPF EID, FMA: 0x10000
  Up: 00:34:14
  RPF-ID: 0, Encap-ID: 1
  Incoming Interface List
    GigabitEthernet0/0/0/0 Flags: A, Up: 00:34:14
  Outgoing Interface List
    LmdtA Flags: F LMI TR, Up: 00:34:14, Head LSM-ID: 0x00001

      
RP/0/0/CPU0:PE2#sh mpl mldp database 0x00001
Fri Jan 29 21:08:35.777 UTC
mLDP database
LSM-ID: 0x00001  Type: P2MP  Uptime: 00:38:24
  FEC Root           : 2.2.2.2 (we are the root)
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    LDP 10.10.10.10:0  Uptime: 00:34:45
      Next Hop         : 10.0.102.10
      Interface        : GigabitEthernet0/0/0/2 
      Remote label (D) : 24003             
    PIM MDT            Uptime: 00:38:24
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)
```

{% tabs %}
{% tab title="PE1" %}
```
RP/0/0/CPU0:PE1#sh mpl forwarding 
Fri Jan 29 21:11:56.563 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24000  Unlabelled  mLDP/IR: 0x00001
24001  24000       2.2.2.2/32         Gi0/0/0/1    10.0.101.10     46536       
24002  24001       3.3.3.3/32         Gi0/0/0/1    10.0.101.10     4896        
24003  Pop         10.10.10.10/32     Gi0/0/0/1    10.0.101.10     5028        
24004  Pop         10.0.102.0/24      Gi0/0/0/1    10.0.101.10     0           
24005  Pop         10.0.103.0/24      Gi0/0/0/1    10.0.101.10     0           
24006  Unlabelled  mLDP/IR: 0x00002
24007  Unlabelled  192.168.0.1/32[V]  Gi0/0/0/0    192.168.1.2     0           
24008  Aggregate   A: Per-VRF Aggr[V] A                            0           
24009  Unlabelled  192.168.101.1/32[V]   \
                                      Gi0/0/0/0    192.168.1.2     0
```
{% endtab %}

{% tab title="P" %}
```
RP/0/0/CPU0:P#sh mpl forwarding 
Fri Jan 29 21:13:01.089 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24000  Pop         2.2.2.2/32         Gi0/0/0/2    10.0.102.2      98182       
24001  Pop         3.3.3.3/32         Gi0/0/0/3    10.0.103.3      12794       
24002  Pop         1.1.1.1/32         Gi0/0/0/1    10.0.101.1      13465       
24003  24006       mLDP/IR: 0x00001   Gi0/0/0/1    10.0.101.1      0           
       24006       mLDP/IR: 0x00001   Gi0/0/0/3    10.0.103.3      0
```
{% endtab %}

{% tab title="PE2" %}
```
RP/0/0/CPU0:PE2#sh mpl forwarding 
Fri Jan 29 21:13:20.238 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24000  24003       mLDP/IR: 0x00001   Gi0/0/0/2    10.0.102.10     0           
24001  Pop         10.10.10.10/32     Gi0/0/0/2    10.0.102.10     4684        
24002  Pop         10.0.101.0/24      Gi0/0/0/2    10.0.102.10     0           
24003  Pop         10.0.103.0/24      Gi0/0/0/2    10.0.102.10     0           
24004  24001       3.3.3.3/32         Gi0/0/0/2    10.0.102.10     4475        
24005  Aggregate   A: Per-VRF Aggr[V] A                            83040       
24006  24002       1.1.1.1/32         Gi0/0/0/2    10.0.102.10     496800
```
{% endtab %}

{% tab title="PE3" %}
```
RP/0/0/CPU0:PE3#sh mpl forwarding 
Fri Jan 29 21:14:19.354 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24000  Unlabelled  mLDP/IR: 0x00001
24001  24000       2.2.2.2/32         Gi0/0/0/3    10.0.103.10     46750       
24002  Pop         10.10.10.10/32     Gi0/0/0/3    10.0.103.10     5216        
24003  Pop         10.0.101.0/24      Gi0/0/0/3    10.0.103.10     0           
24004  Pop         10.0.102.0/24      Gi0/0/0/3    10.0.103.10     0           
24005  24002       1.1.1.1/32         Gi0/0/0/3    10.0.103.10     5217        
24006  Unlabelled  mLDP/IR: 0x00002
24007  Unlabelled  192.168.0.3/32[V]  Gi0/0/0/0    192.168.3.2     0           
24008  Aggregate   A: Per-VRF Aggr[V] A                            0           
24009  Unlabelled  192.168.101.3/32[V]   \
                                      Gi0/0/0/0    192.168.3.2     0
```
{% endtab %}
{% endtabs %}

Let's follow the data-plane for group 232.1.1.1 starting from ingress PE2 to P and lastly to PE1 and PE3. Most interesting output is from P. As you can see P router replicates traffic which arrives with label `24003` to both PE1 and PE3. That's the heart of mVPN MLDP data-plane behaviour.

{% tabs %}
{% tab title="PE2" %}
```
RP/0/0/CPU0:PE2#sh mpl mldp database 
Fri Jan 29 21:28:50.384 UTC
mLDP database
LSM-ID: 0x00001  Type: P2MP  Uptime: 00:58:39
  FEC Root           : 2.2.2.2 (we are the root)
  Opaque decoded     : [global-id 1]
  Upstream neighbor(s) :
    None
  Downstream  client(s): 
    LDP 10.10.10.10:0  Uptime: 00:55:00
      Next Hop         : 10.0.102.10
      Interface        : GigabitEthernet0/0/0/2 
      Remote label (D) : 24003             
    PIM MDT            Uptime: 00:58:39
      Egress intf     : LmdtA
      Table ID        : IPv4: 0xe0000010 IPv6: 0xe0800010
      HLI             : 0x00001
      Ingress         : Yes
      PPMP            : Yes
      Local Label     : 24000 (internal)

RP/0/0/CPU0:PE2#sh mpl forwarding labels 24000
Fri Jan 29 21:31:04.805 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24000  24003       mLDP/IR: 0x00001   Gi0/0/0/2    10.0.102.10     0
```
{% endtab %}

{% tab title="P" %}
```
RP/0/0/CPU0:P#sh mpl forwarding labels 24003
Fri Jan 29 21:29:59.719 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24003  24006       mLDP/IR: 0x00001   Gi0/0/0/1    10.0.101.1      0           
       24006       mLDP/IR: 0x00001   Gi0/0/0/3    10.0.103.3      0
```
{% endtab %}

{% tab title="PE1" %}
```
RP/0/0/CPU0:PE1#sh mpl forwarding labels 24006
Fri Jan 29 21:32:03.291 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24006  Unlabelled  mLDP/IR: 0x00002


```
{% endtab %}

{% tab title="PE3" %}
```
RP/0/0/CPU0:PE3#sh mpl forwarding labels 24006
Fri Jan 29 21:34:41.230 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24006  Unlabelled  mLDP/IR: 0x00002
```
{% endtab %}
{% endtabs %}

Following output show what happens on P router when there's only one receiver behind PE1. CE3 is not requesting 232.1.1.1. So now P router does not replicate traffic ingressing the router with label `24003`.

```
RP/0/0/CPU0:P#sh mpl forwarding 
Sat Jan 30 17:50:55.090 UTC
Local  Outgoing    Prefix             Outgoing     Next Hop        Bytes       
Label  Label       or ID              Interface                    Switched    
------ ----------- ------------------ ------------ --------------- ------------
24000  Pop         2.2.2.2/32         Gi0/0/0/2    10.0.102.2      1400279     
24001  Pop         3.3.3.3/32         Gi0/0/0/3    10.0.103.3      401639      
24002  Pop         1.1.1.1/32         Gi0/0/0/1    10.0.101.1      402253      
24003  24006       mLDP/IR: 0x00001   Gi0/0/0/1    10.0.101.1      0
```

### 3. No receivers, there's an active source

Actually there's nothing interesting in this case. Ingress PE has no parties interested in receiving `232.1.1.1`. Thus it just drops incoming stream.

* [ ] Check CLI
* [ ] Get a bit deeper.

```
```

### 4. Active receivers and active source

{% tabs %}
{% tab title="PE1" %}
```
RP/0/0/CPU0:PE1#sh bgp vrf A ipv4 mvpn 
Fri Jan 29 14:06:21.965 UTC
BGP VRF A, state: Active
BGP Route Distinguisher: 1.1.1.1:0
VRF ID: 0x60000001
BGP router identifier 1.1.1.1, local AS number 1
Non-stop routing is enabled
BGP table state: Active
Table ID: 0x0   RD version: 26
BGP main routing table version 26
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
Fri Jan 29 14:07:02.592 UTC
BGP VRF A, state: Active
BGP Route Distinguisher: 2.2.2.2:0
VRF ID: 0x60000001
BGP router identifier 2.2.2.2, local AS number 1
Non-stop routing is enabled
BGP table state: Active
Table ID: 0x0   RD version: 43
BGP main routing table version 43
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
Fri Jan 29 14:07:39.700 UTC
BGP VRF A, state: Active
BGP Route Distinguisher: 3.3.3.3:0
VRF ID: 0x60000001
BGP router identifier 3.3.3.3, local AS number 1
Non-stop routing is enabled
BGP table state: Active
Table ID: 0x0   RD version: 28
BGP main routing table version 28
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

## PE-CE uses ASM

### No receivers, no active sources

```
```

### Active receiver, no active sources

```
```

### No receivers, there's an active source

```
```

## mVPN interoperability issues

Several vendors depending on a software version are using pre-RFC encoding for `C-Mcast Import RT`. In this case you won't find a correct extended community in VPNv4 updates. Instead there will be community which Cisco interprets as L2VPN AGI.

### Nokia: wrong extended community encoding

Below you can find CLI to switch to RFC encoding.

```
BGP routing table entry for 555:95863000:10.90.5.128/26, version 24415
Paths: (2 available, best #2, table 555-VRF)
  Not advertised to any peer
  Refresh Epoch 1
  65000
    10.79.0.3 (metric 21570) (via default) from 10.79.192.245 (10.79.192.245)
      Origin IGP, metric 20, localpref 300, valid, internal
      Extended Community: SoO:555:324471 RT:555:95863000
        MVPN AS:555:0.0.0.0 L2VPN AGI:10.79.0.3:345
      Originator: 10.79.0.3, Cluster list: 10.79.192.245
      mpls labels in/out nolabel/262034
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  65000
    10.79.0.3 (metric 21570) (via default) from 10.79.192.241 (10.79.192.241)
      Origin IGP, metric 20, localpref 300, valid, internal, best
      Extended Community: SoO:555:324471 RT:555:95863000
        MVPN AS:555:0.0.0.0 L2VPN AGI:10.79.0.3:345
      Originator: 10.79.0.3, Cluster list: 10.79.192.241
      mpls labels in/out nolabel/262034
      rx pathid: 0, tx pathid: 0x0
```

{% tabs %}
{% tab title="Nokia" %}
```
config>router>bgp# mvpn-vrf-import-subtype-new
```
{% endtab %}
{% endtabs %}

![A screenshot from Nokia's documentation](../.gitbook/assets/nokia-screenshot.png)

After you apply it the encoding for extended community looks like the one below:

```yaml
DRP/0/3/CPU0:meltdown-drp#show bgp vpnv4 unicast vrf one 10.2.2.0/24
BGP routing table entry for 10.2.2.0/24, Route Distinguisher: 1:2
Versions:
  Process           bRIB/RIB  SendTblVer
  Speaker                128         128
    Local Label: 16000
Last Modified: May  5 08:37:28.075 for 03:52:19
Paths: (1 available, best #1)
  Advertised to peers (in unique update groups):
    10.1.100.7      
  Path #1: Received by speaker 0
  Advertised to peers (in unique update groups):
    10.1.100.7      
  Local
    0.0.0.0 from 0.0.0.0 (10.1.100.2)
      Origin incomplete, metric 0, localpref 100, weight 32768, valid, redistributed, best, group-best, import-candidate
      Received Path ID 0, Local Path ID 1, version 128
      Extended community: VRF Route Import:10.1.100.2:2 Source AS:1:0 RT:1:1
```

### Juniper: mVPN Wildcard Type 3 wrong encoding

[RFC6625](https://tools.ietf.org/html/rfc6625) defines a new zero-length source and group addresses for mVPN Type 3 BGP Update.

```
   This document specifies that a "zero-length" source
   or group represents the corresponding wildcard.  Specifically,

      - A source wildcard is encoded as a zero-length source field.
        That is, the "multicast source length" field contains the value
        0x00, and the "multicast source" field is omitted.
      - A group wildcard is encoded as a zero-length group field.  That
        is, the "multicast group length" field contains the value 0x00,
        and the "multicast group" field is omitted.
```

#### Direct iBGP between two Cisco NCS5500

172.16.1.44 (NCS5500) originates mVPN Type 3 and advertises it directly to 172.16.1.44 (NCS5500).

Raw BGP message:

```
ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
00 73 02 00 00 00 5c 90 0e 00 19 00 01 05 04 ac
10 01 2c 00 03 0e 00 00 00 01 00 00 04 93 00 00
ac 10 01 2c 40 01 01 00 40 02 00 40 05 04 00 00
00 64 c0 08 04 ff ff ff 01 c0 10 08 00 02 00 01
00 00 00 75 c0 16 16 00 02 00 00 00 06 00 01 04
ac 10 01 2c 00 07 01 00 04 00 00 00 02 c0 46 03
05 df d1
```

Decoded message:

```
MP_REACH_NLRI
0x900e001900010504AC10012C00030E00000001000004930000AC10012C

ATTRIBUTE FLAG:        0x90
ATTRIBUTE FLAG binary:    10010000
    Bit 0, the Optional bit, is 1 so this is an optional attribute
    Bit 1, the Transitive bit, is 0 so this is a non-transitive attribute
    Bit 2, the Partial bit, is not set
    Bit 3, the Extended Length Bit, is 1 so the length field is 2 bytes
    The lower-order four bits of the Attribute Flag are unused and are set to 0000

ATTRIBUTE TYPE:        0x0E    - 14 
ATTRIBUTE LENGTH:    0x0019    - 25 bytes
ATTRIBUTE CONTENT:    0x00010504AC10012C00030E00000001000004930000AC10012C

    AFI:        1 (0x0001)
    Sub AFI:    5 (0x05)
    NEXTHOP Length:    4 (0x04) bytes
    NEXTHOP:    172.16.1.44
    Numb of SNPAs:    0 (0x00)

    Route type:     3    (0x03) - S-PMSI A-D route
    NLRI Length:     14 bytes    (0x0E)
    RD: 1:1171 (0x0000 - 0x0001:0x00000493)
    Multicast Source Length = 0 (0x00)
    Multicast Source Address = 
    Multicast Group Length = 0 (0x00)
    Multicast Group Address = 
    Originating Router IP Address = 172.16.1.44
```

#### iBGP with Juniper RR

172.16.1.44 (NCS5500) originates mVPN Type 3 and advertises it to a route-reflector 172.16.1.41 (Juniper). RR then reflects the route to 172.16.1.43 (NCS5500).

Raw BGP message:

```
FF FF FF FF  FF FF FF FF  FF FF FF FF  FF FF FF FF  
00 89 02 00  00 00 72 40  01 01 00 40  02 00 40 05  
04 00 00 00  64 C0 08 04  FF FF FF 01  C0 10 08 00  
02 00 01 00  00 00 75 80  09 04 AC 10  01 2C 80 0A  
04 AC 10 01  29 C0 16 16  00 02 00 00  00 06 00 01  
04 AC 10 01  2C 00 07 01  00 04 00 00  00 02 E0 46  
03 05 DF D1  90 0E 00 21  00 01 05 04  AC 10 01 2C  
00 03 16 00  00 00 01 00  00 04 93 00  00 00 00 00  
00 00 00 00  00 AC 10 01  2C
```

Decoded message:

```
MP_REACH_NLRI
0x900e002100010504AC10012C000316000000010000049300000000000000000000AC10012C

ATTRIBUTE FLAG:        0x90
ATTRIBUTE FLAG binary:    10010000
    Bit 0, the Optional bit, is 1 so this is an optional attribute
    Bit 1, the Transitive bit, is 0 so this is a non-transitive attribute
    Bit 2, the Partial bit, is not set
    Bit 3, the Extended Length Bit, is 1 so the length field is 2 bytes
    The lower-order four bits of the Attribute Flag are unused and are set to 0000

ATTRIBUTE TYPE:        0x0E    - 14 
ATTRIBUTE LENGTH:    0x0021    - 33 bytes
ATTRIBUTE CONTENT:    0x00010504AC10012C000316000000010000049300000000000000000000AC10012C

    AFI:        1 (0x0001)
    Sub AFI:    5 (0x05)
    NEXTHOP Length:    4 (0x04) bytes
    NEXTHOP:    172.16.1.44
    Numb of SNPAs:    0 (0x00)

    Route type:     3    (0x03) - S-PMSI A-D route
    NLRI Length:     22 bytes    (0x16)
    RD: 1:1171 (0x0000 - 0x0001:0x00000493)
    Multicast Source Length = 0 (0x00)
    Multicast Source Address = 
    Multicast Group Length = 0 (0x00)
    Multicast Group Address = 
    Originating Router IP Address = 0.0.0.0
    Route type:     0    (0x00) - Unknown
    NLRI Length:     0 bytes    (0x00)
    ERROR: NLRI length is 0
    ERROR: data is 0000AC10012C
```

#### Key findings

1. Juniper RR receives the correct mVPN Type 3 Wildcard update with `Multicast Source Length` and `Multicast Group Length` set to `0x00`. `Multicast Source Address` and `Multicast Group Address` are omitted.
2. Juniper reflects a modified update with `Multicast Source Address` and `Multicast Group Address` fields set to `0.0.0.0`.
3. To reflect the changed NRLI length Juniper adjusts the `ATTRIBUTE LENGTH` and `NLRI Length` fields accordingly.
4. NCS5500 receives the corrupted update and reads it based on RFC6625 encoding.

```yaml
Cisco:   0x900e001900010504AC10012C00030E00000001000004930000                AC10012C
Juniper: 0x900e002100010504AC10012C000316000000010000049300000000000000000000AC10012C
```

#### Workaround on Juniper

```
set routing-options multicast omit-wildcard-address
```

## Links

{% embed url="https://tools.ietf.org/html/rfc6514" %}


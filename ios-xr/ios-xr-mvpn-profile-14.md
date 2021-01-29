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

In short it means that first hop routers \(CE1 and CE3\) receive IGMPv3 join where both source address and multicast group are specified. mVPN involves routes Type 1, 3 and 7 in this case.

| Type | Name | Definition |
| :--- | :--- | :--- |
| Type 1 | Intra-AS I-PMSI A-D route |  |
| Type 2 | Inter-AS I-PMSI A-D route |  |
| Type 3 | S-PMSI A-D route |  |
| Type 4 | Leaf A-D route |  |
| Type 5 | Source Active A-D route |  |
| Type 6 | Shared Tree Join route |  |
| Type 7 | Source Tree Join route |  |

### 1. No receivers, no active sources

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

### 2. Active receiver, no active sources

When egress PEs receive PIM Join  \(C-S, C-G\) they generate Type 7 to express the presence of receiver behind. As you can see from PE2 output \(ingress PE\) it gets Type 7 routes from both egress PEs.

#### IGMP state on CE routers

{% tabs %}
{% tab title="CE1" %}
```text
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
```
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

{% tabs %}
{% tab title="PE1" %}
```text
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
```
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
```
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
```text
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

#### Type 7 in more details

```text
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

```text
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

### 3. No receivers, there's an active source

Actually there's nothing interesting in this case. Ingress PE has no parties interested in receiving `232.1.1.1`. Thus it just drops incoming stream.

* [ ] Check CLI
* [ ] Get a bit deeper.

```text

```

### 4. Active receivers and active source

{% tabs %}
{% tab title="PE1" %}
```text
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

```text

```

### Active receiver, no active sources

```text

```

### No receivers, there's an active source

```text

```

## mVPN interoperability issues

Several vendors depending on a software version are using pre-RFC encoding for `C-Mcast Import RT`. In this case you won't find a correct extended community in VPNv4 updates. Instead there will be community which Cisco interprets as L2VPN AGI.

#### Wrong encoding

Below you can find CLI to switch to RFC encoding.

```text
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
```text

```
{% endtab %}

{% tab title="Juniper" %}
```

```
{% endtab %}
{% endtabs %}




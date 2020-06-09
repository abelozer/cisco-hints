# Test 2.1: ISIS Basic Functionality

{% hint style="info" %}
Be aware of IS-IS multitopology support on Huawei and Juniper.
{% endhint %}

## **ISIS Configuration Template**

```erlang
router isis {{ ISIS_PROCESS_NAME }}
 is-type level-2-only
 net {{ ISIS_NET }}
 address-family ipv4 unicast
  metric-style wide
  router-id Loopback0
  mpls ldp auto-config
 !
 address-family ipv6 unicast
  metric-style wide
  router-id Loopback0
 !
 interface Loopback0
  passive
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
 interface {{ NNI_IFACE }}
  point-to-point
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
```

## **ISIS Configuration Example**

```erlang
router isis CORE
 is-type level-2-only
 net 49.1720.1600.1013.00
 address-family ipv4 unicast
  metric-style wide
  router-id Loopback0
  mpls ldp auto-config
 !
 address-family ipv6 unicast
  metric-style wide
  router-id Loopback0
 !
 interface Loopback0
  passive
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
 interface GigabitEthernet0/0/0/0
  point-to-point
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
 interface GigabitEthernet0/0/0/1
  point-to-point
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
 interface GigabitEthernet0/0/0/2
  point-to-point
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
 interface GigabitEthernet0/0/0/3
  point-to-point
  address-family ipv4 unicast
  !
  address-family ipv6 unicast
  !
 !
!
```

## **ISIS Verification**

```erlang
RP/0/0/CPU0:P3#show isis interface brief
Wed Jun  3 12:45:10.927 UTC

IS-IS CORE Interfaces
    Interface      All     Adjs    Adj Topos  Adv Topos  CLNS   MTU    Prio  
                   OK    L1   L2    Run/Cfg    Run/Cfg                L1   L2
-----------------  ---  ---------  ---------  ---------  ----  ----  --------
Lo0                Yes    -    -      0/0        1/1     No       -    -    -
Gi0/0/0/0          Yes    -    1      1/1        1/1     Up    1497    -    -
Gi0/0/0/1          Yes    -    1      1/1        1/1     Up    1497    -    -
Gi0/0/0/2          Yes    -    1      1/1        1/1     Up    1497    -    -
Gi0/0/0/3          Yes    -    1      1/1        1/1     Up    1497    -    -
```

```erlang
RP/0/0/CPU0:P3#show isis interface
Wed Jun  3 12:43:32.664 UTC

IS-IS CORE Interfaces
Loopback0                   Enabled
  Adjacency Formation:      Disabled (Passive in IS-IS cfg)
  Prefix Advertisement:     Enabled
  IPv4 BFD:                 Disabled
  IPv6 BFD:                 Disabled
  BFD Min Interval:         150
  BFD Multiplier:           3
  Bandwidth:                0

  Circuit Type:             level-2-only (Interface circuit type is level-1-2)
  Media Type:               Loop
  Circuit Number:           0
  IPv4 Unicast Topology:    Enabled
    Adjacency Formation:    Disabled (Intf passive in IS-IS cfg)
    Prefix Advertisement:   Running
    Metric (L1/L2):         0/0
    Weight (L1/L2):         0/0
    MPLS Max Label Stack(PRI/BKP/SRTE):2/2/10
    MPLS LDP Sync (L1/L2):  Disabled/Disabled
    FRR (L1/L2):            L1 Not Enabled     L2 Not Enabled
      FRR Type:             None               None

    IPv4 Address Family:    Enabled
      Protocol State:       Up
      Forwarding Address(es):Unknown (Intf passive in IS-IS cfg)
      Global Prefix(es):    172.16.1.13/32


GigabitEthernet0/0/0/0      Enabled
  Adjacency Formation:      Enabled
  Prefix Advertisement:     Enabled
  IPv4 BFD:                 Disabled
  IPv6 BFD:                 Disabled
  BFD Min Interval:         150
  BFD Multiplier:           3
  Bandwidth:                1000000

  Circuit Type:             level-2-only (Interface circuit type is level-1-2)
  Media Type:               P2P
  Circuit Number:           0
  Extended Circuit Number:  0
  Next P2P IIH in:          2 s
  LSP Rexmit Queue Size:    0

  Level-2
    Adjacency Count:        1
    LSP Pacing Interval:    33 ms
    PSNP Entry Queue Size:  0
    Hello Interval:         10 s
    Hello Multiplier:       3

  CLNS I/O
    Protocol State:         Up
    MTU:                    1497
    SNPA:                   5254.001f.c45a
    Layer-2 MCast Groups Membership:
      All ISs:              Yes

  IPv4 Unicast Topology:    Enabled
    Adjacency Formation:    Running
    Prefix Advertisement:   Running
    Metric (L1/L2):         10/10
    Weight (L1/L2):         0/0
    MPLS Max Label Stack(PRI/BKP/SRTE):1/3/10
    MPLS LDP Sync (L1/L2):  Disabled/Disabled
    FRR (L1/L2):            L1 Not Enabled     L2 Not Enabled
      FRR Type:             None               None

    IPv4 Address Family:    Enabled
      Protocol State:       Up
      Forwarding Address(es):172.16.32.2
      Global Prefix(es):    172.16.32.2/31

    LSP transmit timer expires in 0 ms
    LSP transmission is idle
    Can send up to 9 back-to-back LSPs in the next 0 ms

GigabitEthernet0/0/0/1      Enabled
  Adjacency Formation:      Enabled
  Prefix Advertisement:     Enabled
  IPv4 BFD:                 Disabled
  IPv6 BFD:                 Disabled
  BFD Min Interval:         150
  BFD Multiplier:           3
  Bandwidth:                1000000

  Circuit Type:             level-2-only (Interface circuit type is level-1-2)
  Media Type:               P2P
  Circuit Number:           0
  Extended Circuit Number:  4
  Next P2P IIH in:          5 s
  LSP Rexmit Queue Size:    0

  Level-2
    Adjacency Count:        1
    LSP Pacing Interval:    33 ms
    PSNP Entry Queue Size:  0
    Hello Interval:         10 s
    Hello Multiplier:       3

  CLNS I/O
    Protocol State:         Up
    MTU:                    1497
    SNPA:                   5254.0005.510a
    Layer-2 MCast Groups Membership:
      All ISs:              Yes

  IPv4 Unicast Topology:    Enabled
    Adjacency Formation:    Running
    Prefix Advertisement:   Running
    Metric (L1/L2):         10/10
    Weight (L1/L2):         0/0
    MPLS Max Label Stack(PRI/BKP/SRTE):1/3/10
    MPLS LDP Sync (L1/L2):  Disabled/Disabled
    FRR (L1/L2):            L1 Not Enabled     L2 Not Enabled
      FRR Type:             None               None

    IPv4 Address Family:    Enabled
      Protocol State:       Up
      Forwarding Address(es):172.16.32.0
      Global Prefix(es):    172.16.32.0/31

    LSP transmit timer expires in 0 ms
    LSP transmission is idle
    Can send up to 9 back-to-back LSPs in the next 0 ms

GigabitEthernet0/0/0/2      Enabled
  Adjacency Formation:      Enabled
  Prefix Advertisement:     Enabled
  IPv4 BFD:                 Disabled
  IPv6 BFD:                 Disabled
  BFD Min Interval:         150
  BFD Multiplier:           3
  Bandwidth:                1000000

  Circuit Type:             level-2-only (Interface circuit type is level-1-2)
  Media Type:               P2P
  Circuit Number:           0
  Extended Circuit Number:  4
  Next P2P IIH in:          5 s
  LSP Rexmit Queue Size:    0

  Level-2
    Adjacency Count:        1
    LSP Pacing Interval:    33 ms
    PSNP Entry Queue Size:  0
    Hello Interval:         10 s
    Hello Multiplier:       3

  CLNS I/O
    Protocol State:         Up
    MTU:                    1497
    SNPA:                   5254.000c.345a
    Layer-2 MCast Groups Membership:
      All ISs:              Yes

  IPv4 Unicast Topology:    Enabled
    Adjacency Formation:    Running
    Prefix Advertisement:   Running
    Metric (L1/L2):         10/10
    Weight (L1/L2):         0/0
    MPLS Max Label Stack(PRI/BKP/SRTE):1/3/10
    MPLS LDP Sync (L1/L2):  Disabled/Disabled
    FRR (L1/L2):            L1 Not Enabled     L2 Not Enabled
      FRR Type:             None               None

    IPv4 Address Family:    Enabled
      Protocol State:       Up
      Forwarding Address(es):172.16.32.14
      Global Prefix(es):    172.16.32.14/31

    LSP transmit timer expires in 0 ms
    LSP transmission is idle
    Can send up to 9 back-to-back LSPs in the next 0 ms

GigabitEthernet0/0/0/3      Enabled
  Adjacency Formation:      Enabled
  Prefix Advertisement:     Enabled
  IPv4 BFD:                 Disabled
  IPv6 BFD:                 Disabled
  BFD Min Interval:         150
  BFD Multiplier:           3
  Bandwidth:                1000000

  Circuit Type:             level-2-only (Interface circuit type is level-1-2)
  Media Type:               P2P
  Circuit Number:           0
  Extended Circuit Number:  4
  Next P2P IIH in:          5 s
  LSP Rexmit Queue Size:    0

  Level-2
    Adjacency Count:        1
    LSP Pacing Interval:    33 ms
    PSNP Entry Queue Size:  0
    Hello Interval:         10 s
    Hello Multiplier:       3

  CLNS I/O
    Protocol State:         Up
    MTU:                    1497
    SNPA:                   5254.000d.20d7
    Layer-2 MCast Groups Membership:
      All ISs:              Yes

  IPv4 Unicast Topology:    Enabled
    Adjacency Formation:    Running
    Prefix Advertisement:   Running
    Metric (L1/L2):         10/10
    Weight (L1/L2):         0/0
    MPLS Max Label Stack(PRI/BKP/SRTE):1/3/10
    MPLS LDP Sync (L1/L2):  Disabled/Disabled
    FRR (L1/L2):            L1 Not Enabled     L2 Not Enabled
      FRR Type:             None               None

    IPv4 Address Family:    Enabled
      Protocol State:       Up
      Forwarding Address(es):172.16.32.13
      Global Prefix(es):    172.16.32.12/31

    LSP transmit timer expires in 0 ms
    LSP transmission is idle
    Can send up to 9 back-to-back LSPs in the next 0 ms
```

```erlang
RP/0/0/CPU0:P3#show isis neighbors
Wed Jun  3 12:45:36.415 UTC

IS-IS CORE neighbors:
System Id      Interface        SNPA           State Holdtime Type IETF-NSF
PE2            Gi0/0/0/2        *PtoP*         Up    22       L2   Capable
PE1            Gi0/0/0/3        *PtoP*         Up    28       L2   Capable
P4             Gi0/0/0/1        *PtoP*         Up    26       L2   Capable
P1             Gi0/0/0/0        *PtoP*         Up    27       L2   Capable

Total neighbor count: 4
```

```erlang
RP/0/0/CPU0:P3#show isis adjacency
Wed Jun  3 12:46:00.783 UTC

IS-IS CORE Level-2 adjacencies:
System Id      Interface        SNPA           State Hold Changed  NSF IPv4 IPv6
                                                                       BFD  BFD
PE2            Gi0/0/0/2        *PtoP*         Up    22   02:13:14 Yes None None
PE1            Gi0/0/0/3        *PtoP*         Up    27   02:13:14 Yes None None
P4             Gi0/0/0/1        *PtoP*         Up    28   00:28:01 Yes None None
P1             Gi0/0/0/0        *PtoP*         Up    22   02:13:14 Yes None None

Total adjacency count: 4
```

```erlang
RP/0/0/CPU0:P3#show isis route
Wed Jun  3 12:46:44.120 UTC

IS-IS CORE IPv4 Unicast routes

Codes: L1 - level 1, L2 - level 2, ia - interarea (leaked into level 1)
       df - level 1 default (closest attached router), su - summary null
       C - connected, S - static, R - RIP, B - BGP, O - OSPF
       E - EIGRP, A - access/subscriber, M - mobile, a - application
       i - IS-IS (redistributed from another instance)

Maximum parallel path count: 8

L2 1.1.1.1/32 [30/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 172.16.1.1/32 [30/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 172.16.1.11/32 [20/115]
     via 172.16.32.3, GigabitEthernet0/0/0/0, P1, Weight: 0
L2 172.16.1.12/32 [30/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
     via 172.16.32.3, GigabitEthernet0/0/0/0, P1, Weight: 0
C  172.16.1.13/32
     is directly connected, Loopback0
L2 172.16.1.14/32 [10/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 172.16.1.21/32 [10/115]
     via 172.16.32.12, GigabitEthernet0/0/0/3, PE1, Weight: 0
L2 172.16.1.22/32 [10/115]
     via 172.16.32.15, GigabitEthernet0/0/0/2, PE2, Weight: 0
L2 172.16.1.23/32 [30/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 172.16.1.24/32 [30/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 172.16.1.25/32 [20/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
C  172.16.32.0/31
     is directly connected, GigabitEthernet0/0/0/1
C  172.16.32.2/31
     is directly connected, GigabitEthernet0/0/0/0
L2 172.16.32.4/31 [20/115]
     via 172.16.32.3, GigabitEthernet0/0/0/0, P1, Weight: 0
L2 172.16.32.6/31 [20/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 172.16.32.8/31 [20/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 172.16.32.10/31 [20/115]
     via 172.16.32.12, GigabitEthernet0/0/0/3, PE1, Weight: 0
     via 172.16.32.3, GigabitEthernet0/0/0/0, P1, Weight: 0
C  172.16.32.12/31
     is directly connected, GigabitEthernet0/0/0/3
C  172.16.32.14/31
     is directly connected, GigabitEthernet0/0/0/2
L2 172.16.32.16/31 [20/115]
     via 172.16.32.15, GigabitEthernet0/0/0/2, PE2, Weight: 0
     via 172.16.32.3, GigabitEthernet0/0/0/0, P1, Weight: 0
L2 172.16.32.18/31 [30/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
     via 172.16.32.3, GigabitEthernet0/0/0/0, P1, Weight: 0
L2 172.16.32.20/31 [30/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
     via 172.16.32.3, GigabitEthernet0/0/0/0, P1, Weight: 0
L2 172.16.32.22/31 [30/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
     via 172.16.32.3, GigabitEthernet0/0/0/0, P1, Weight: 0
L2 172.16.32.24/31 [20/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 172.16.32.26/31 [20/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 172.16.32.28/31 [20/115]
     via 172.16.32.1, GigabitEthernet0/0/0/1, P4, Weight: 0
```

```erlang
RP/0/0/CPU0:P3#show route summary
Wed Jun  3 12:47:17.308 UTC
Route Source                     Routes     Backup     Deleted     Memory(bytes)
local                            5          0          0           800
connected                        4          1          0           800
isis CORE                        21         4          0           4432
bgp 100                          0          0          0           0
dagr                             0          0          0           0
Total                            30         5          0           6032
```

```erlang
RP/0/0/CPU0:P3#show isis ipv6 route
Mon Jun  8 10:00:44.604 UTC

IS-IS CORE IPv6 Unicast routes

Codes: L1 - level 1, L2 - level 2, ia - interarea (leaked into level 1)
       df - level 1 default (closest attached router), su - summary null
       C - connected, S - static, R - RIP, B - BGP, O - OSPF
       E - EIGRP, A - access/subscriber, M - mobile, a - application
       i - IS-IS (redistributed from another instance)

Maximum parallel path count: 8

L2 2001:db8::1/128 [30/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8::11/128 [30/115]
     via fe80::5054:ff:fe02:4097, GigabitEthernet0/0/0/2, PE2, Weight: 0
     via fe80::5054:ff:fe1e:4e12, GigabitEthernet0/0/0/3, PE1, Weight: 0
L2 2001:db8::12/128 [30/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
C  2001:db8::13/128
     is directly connected, Loopback0
L2 2001:db8::14/128 [10/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8::21/128 [10/115]
     via fe80::5054:ff:fe1e:4e12, GigabitEthernet0/0/0/3, PE1, Weight: 0
L2 2001:db8::22/128 [10/115]
     via fe80::5054:ff:fe02:4097, GigabitEthernet0/0/0/2, PE2, Weight: 0
L2 2001:db8::23/128 [30/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8::24/128 [30/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8::25/128 [20/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8:114::/120 [20/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8:1112::/120 [30/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
     via fe80::5054:ff:fe02:4097, GigabitEthernet0/0/0/2, PE2, Weight: 0
     via fe80::5054:ff:fe1e:4e12, GigabitEthernet0/0/0/3, PE1, Weight: 0
C  2001:db8:1113::/120
     is directly connected, GigabitEthernet0/0/0/0
L2 2001:db8:1121::/120 [20/115]
     via fe80::5054:ff:fe1e:4e12, GigabitEthernet0/0/0/3, PE1, Weight: 0
L2 2001:db8:1122::/120 [20/115]
     via fe80::5054:ff:fe02:4097, GigabitEthernet0/0/0/2, PE2, Weight: 0
L2 2001:db8:1214::/120 [20/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8:1223::/120 [30/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8:1224::/120 [30/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8:1225::/120 [30/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
C  2001:db8:1314::/120
     is directly connected, GigabitEthernet0/0/0/1
C  2001:db8:1321::/120
     is directly connected, GigabitEthernet0/0/0/3
C  2001:db8:1322::/120
     is directly connected, GigabitEthernet0/0/0/2
L2 2001:db8:1423::/120 [20/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8:1424::/120 [20/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
L2 2001:db8:1425::/120 [20/115]
     via fe80::5054:ff:fe10:6a32, GigabitEthernet0/0/0/1, P4, Weight: 0
```

```erlang
RP/0/0/CPU0:P3#show route ipv6 summary
Mon Jun  8 10:01:36.811 UTC
Route Source                     Routes     Backup     Deleted     Memory(bytes)
local                            5          0          0           960
connected                        4          1          0           960
connected l2tpv3_xconnect        0          0          0           0
isis CORE                        20         4          0           5024
Total                            29         5          0           6944
```


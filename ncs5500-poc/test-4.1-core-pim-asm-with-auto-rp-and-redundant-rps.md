# Test 4.1: Core PIM ASM with auto-RP and redundant RPs

## **PIM ASM with Auto-rp configurtation template**

```erlang
multicast-routing
 address-family ipv4
  interface all enable
 !
!
```

## **PIM ASM with auto-rp configurtation example**

```erlang
multicast-routing
 address-family ipv4
  interface all enable
 !
!
```

## **PIM ASM verification**

```text
RP/0/0/CPU0:P3#sho pim interface
Mon Jun  8 13:26:06.440 UTC

PIM interfaces in VRF default
Address               Interface                     PIM  Nbr   Hello  DR    DR
                                                         Count Intvl  Prior

172.16.1.13           Loopback0                     on   1     30     1     this system
172.16.32.2           GigabitEthernet0/0/0/0        on   1     30     1     this system
172.16.32.0           GigabitEthernet0/0/0/1        on   2     30     1     172.16.32.1
172.16.32.14          GigabitEthernet0/0/0/2        on   2     30     1     172.16.32.15
172.16.32.13          GigabitEthernet0/0/0/3        on   2     30     1     this system
```

```erlang
RP/0/0/CPU0:P3#show pim neighbor
Mon Jun  8 13:25:13.014 UTC

PIM neighbors in VRF default
Flag: B - Bidir capable, P - Proxy capable, DR - Designated Router,
      E - ECMP Redirect capable
      * indicates the neighbor created for this router

Neighbor Address             Interface              Uptime    Expires  DR pri   Flags

172.16.32.2*                 GigabitEthernet0/0/0/0 3d23h     00:01:22 1 (DR) B E
172.16.1.13*                 Loopback0              3d23h     00:01:26 1 (DR) B E
172.16.32.0*                 GigabitEthernet0/0/0/1 3d23h     00:01:42 1      B E
172.16.32.1                  GigabitEthernet0/0/0/1 3d23h     00:01:22 1 (DR) B
172.16.32.14*                GigabitEthernet0/0/0/2 3d23h     00:01:22 1      B E
172.16.32.15                 GigabitEthernet0/0/0/2 3d23h     00:01:22 1 (DR) B
172.16.32.12                 GigabitEthernet0/0/0/3 3d23h     00:01:20 1      B
172.16.32.13*                GigabitEthernet0/0/0/3 3d23h     00:01:42 1 (DR) B E
```

```erlang
RP/0/0/CPU0:P3#show pim rp mapping
Mon Jun  8 13:25:32.893 UTC
PIM Group-to-RP Mappings
Group(s) 224.0.0.0/4
  RP 1.1.1.1 (?), v2
    Info source: 1.1.1.1 (?), elected via autorp
      Uptime: 3d23h, expires: 00:03:00
```

```erlang
RP/0/0/CPU0:P3#show mfib interface
Mon Jun  8 13:23:52.929 UTC

Interface : GigabitEthernet0/0/0/0 (Enabled)
SW Mcast pkts in : 0, SW Mcast pkts out : 11204
TTL Threshold : 0
Ref Count : 5

Interface : Loopback0 (Enabled)
SW Mcast pkts in : 0, SW Mcast pkts out : 0
TTL Threshold : 0
Ref Count : 4

Interface : GigabitEthernet0/0/0/1 (Enabled)
SW Mcast pkts in : 11606, SW Mcast pkts out : 0
TTL Threshold : 0
Ref Count : 6

Interface : GigabitEthernet0/0/0/2 (Enabled)
SW Mcast pkts in : 0, SW Mcast pkts out : 11204
TTL Threshold : 0
Ref Count : 5

Interface : GigabitEthernet0/0/0/3 (Enabled)
SW Mcast pkts in : 0, SW Mcast pkts out : 11608
TTL Threshold : 0
Ref Count : 6

Interface : FINT0/0/CPU0 (Disabled)
SW Mcast pkts in : 0, SW Mcast pkts out : 0
TTL Threshold : 0
Ref Count : 2
```


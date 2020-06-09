# Test 3.1: Basic LDP functionality with Huawei and Juniper interop

## **LDP Configuration Template**

### **LDP Template Option 1: auto-config**

```erlang
mpls ldp
 router-id {{ RID }}
 address-family ipv4
  label
   local
    allocate for host-routes
   !
  !
 !
!
!
router isis {{ ISIS_PROCESS_NAME }}
 address-family ipv4 unicast
  mpls ldp auto-config
```

### **LDP Template Option 2: interface listing**

```erlang
mpls ldp
 router-id {{ RID }}
 interface {{ IFACE }}
 address-family ipv4
  label
   local
    allocate for host-routes
   !
  !
 !
!
!
router isis {{ ISIS_PROCESS_NAME }}
 address-family ipv4 unicast
  mpls ldp auto-config
```

## **LDP Configuration Example**

```coffeescript
mpls ldp
 mldp
 !
 router-id 172.16.1.13
 address-family ipv4
  label
   local
    allocate for host-routes
   !
  !
 !
!
router isis CORE
 address-family ipv4 unicast
  mpls ldp auto-config
```

## **LDP Verification**

```erlang
RP/0/0/CPU0:P3#show mpls ldp neighbor
Mon Jun  8 10:07:04.928 UTC

Peer LDP Identifier: 172.16.1.21:0
  TCP connection: 172.16.1.21:62345 - 172.16.1.13:646
  Graceful Restart: No
  Session Holdtime: 180 sec
  State: Oper; Msgs sent/rcvd: 1096/1093; Downstream-Unsolicited
  Up time: 15:33:32
  LDP Discovery Sources:
    IPv4: (1)
      GigabitEthernet0/0/0/3
    IPv6: (0)
  Addresses bound to this peer:
    IPv4: (3)
      172.16.1.21    172.16.32.11   172.16.32.12
    IPv6: (0)

Peer LDP Identifier: 172.16.1.14:0
  TCP connection: 172.16.1.14:19907 - 172.16.1.13:646
  Graceful Restart: No
  Session Holdtime: 180 sec
  State: Oper; Msgs sent/rcvd: 1096/1098; Downstream-Unsolicited
  Up time: 15:33:32
  LDP Discovery Sources:
    IPv4: (1)
      GigabitEthernet0/0/0/1
    IPv6: (0)
  Addresses bound to this peer:
    IPv4: (7)
      172.16.1.14    172.16.32.1    172.16.32.7    172.16.32.8
      172.16.32.24   172.16.32.26   172.16.32.28
    IPv6: (0)

Peer LDP Identifier: 172.16.1.22:0
  TCP connection: 172.16.1.22:21868 - 172.16.1.13:646
  Graceful Restart: No
  Session Holdtime: 180 sec
  State: Oper; Msgs sent/rcvd: 1093/1097; Downstream-Unsolicited
  Up time: 15:33:32
  LDP Discovery Sources:
    IPv4: (1)
      GigabitEthernet0/0/0/2
    IPv6: (0)
  Addresses bound to this peer:
    IPv4: (3)
      172.16.1.22    172.16.32.15   172.16.32.16
    IPv6: (0)
```

```erlang
RP/0/0/CPU0:P3#sh mpl ldp parameters
Mon Jun  8 10:13:56.300 UTC

LDP Parameters:
  Role: Active
  Protocol Version: 1
  Router ID: 172.16.1.13
  Null Label:
    IPv4: Implicit
  Session:
    Hold time: 180 sec
    Keepalive interval: 60 sec
    Backoff: Initial:15 sec, Maximum:120 sec
    Global MD5 password: Disabled
  Discovery:
    Link Hellos:     Holdtime:15 sec, Interval:5 sec
    Targeted Hellos: Holdtime:90 sec, Interval:10 sec
    Quick-start: Enabled (by default)
    Transport address:
      IPv4: 172.16.1.13
  Graceful Restart:
    Disabled
  NSR: Disabled, Not Sync-ed
  Timeouts:
    Housekeeping periodic timer: 10 sec
    Local binding: 300 sec
    Forwarding state in LSD: 15 sec
  Delay in AF Binding Withdrawl from peer: 180 sec
  Max:
    1500 interfaces (1200 attached, 300 TE tunnel), 2000 peers
  OOR state
    Memory: Normal
```


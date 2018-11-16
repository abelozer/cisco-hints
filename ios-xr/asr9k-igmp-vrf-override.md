# IGMP VRF override

## Описание

{% hint style="info" %}
Поддерживается начиная с IOS XR 3.9.0
{% endhint %}

Функционал применим при следующем дизайне: ISP часто используют разные multicast и unicast топологии для сервиса IPTV. Например, одна PE обрабатывает запросы unicast в VRF \(настройки и обновления для приставок, программа передач и т.д.\), другая PE — IGMP и, соответственно, PIM в GRT.

Функционал IGMP VRF Override позволяет на одном PE получать unicast запросы в VRF и транслировать IGMP запросы в GRT. PIM в топологии GRT добавляет интерфейсы VRF в IOL.

{% hint style="info" %}
IGMP VRF Override не поддерживается на интерфейсах BVI.
{% endhint %}

## Настройка

### Настройка интерфейса в VRF

```text
interface {{ MCAST_IFACE }}
 vrf {{ MCAST_VRF_NAME }}
 ipv4 address {{ IPADDR }} {{ SUBNETMASK }}
```

### Настройка DHCP relay

```text
dhcp ipv4
 profile {{ PROFILE_NAME }} relay
  broadcast-flag policy check
  helper-address vrf {{ MCAST_VRF_NAME }} {{ DHCP_SERVER_ADDRESS }}
 !
 interface {{ MCAST_IFACE }} relay profile {{ PROFILE_NAME }}
```

### Настройка политики для IGMP VRF Override

```text
route-policy {{ IGMP_VRF_OVERRIDE_POLICY_NAME }}
 set rpf-topology vrf default
end-policy
```

### Настройка PIM

```text
router pim
 vrf {{ MCAST_VRF_NAME }}
  address-family ipv4
   rpf topology route-policy {{ IGMP_VRF_OVERRIDE_POLICY_NAME }}
  !
 !
```

### Настройка multicast-routing

```text
multicast-routing
 vrf {{ MCAST_VRF_NAME_NAME }}
  address-family ipv4
   interface {{ MCAST_IFACE }} enable
```

## Проверка

```text
RP/0/0/CPU0:x117#sh igmp vrf SMM_VOD groups
IGMP Connected Group Membership
Group Address Interface Uptime Expires Last Reporter
224.0.0.2 GigabitEthernet0/0/0/2.201 00:13:07 never 172.16.200.2
224.0.0.13 GigabitEthernet0/0/0/2.201 00:13:07 never 172.16.200.2
224.0.0.22 GigabitEthernet0/0/0/2.201 00:13:07 never 172.16.200.2
224.0.1.40 GigabitEthernet0/0/0/2.201 00:13:07 never 172.16.200.2
239.255.1.100 GigabitEthernet0/0/0/2.201 00:02:16 never 172.16.200.2
```

```text
RP/0/0/CPU0:x117#sh pim vrf SMM_VOD topology
IP PIM Multicast Topology Table
...
(*,239.255.1.100) SM Up: 00:01:53 RP: 0.0.0.0
JP: Join(now) RPF VRF: default Flags: EX LH DSS
  GigabitEthernet0/0/0/2.201  00:01:53  fwd LI II LH
```

```text
RP/0/0/CPU0:x117#sh pim topology 239.255.1.100 detail
IP PIM Multicast Topology Table
...
(*,239.255.1.100) SM Up: 00:00:43 RP: 192.168.0.18
JP: Join(00:00:06) RPF: GigabitEthernet0/0/0/0,10.0.128.14 Flags: EX LH
Up: MT clr (00:00:00) MDT: JoinSend N, Cache N/N, Misc (0x0,0/0)
Cache: Add 00:00:00, Rem 00:00:00. MT Cnt: Set 0, Unset 0. Joins sent 0
MDT-ifh 0x0/0x0, MT Slot none/ none
RPF-redirect BW usage: 0, Flags: 0x0, ObjID: 0x0
c-multicast-routing: PIM BGPJP: 5d02h
RPF Table: IPv4-Unicast-default
  VRF: SMM_VOD
   GigabitEthernet0/0/0/2.201  00:00:43  fwd LI II LH EX
```

```text
RP/0/0/CPU0:x117#sh mrib route 239.255.1.100
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
(*,239.255.1.100) RPF nbr: 10.0.128.14 Flags: C RPF EX
  Up: 00:01:28
  Incoming Interface List
    GigabitEthernet0/0/0/0 Flags: A NS, Up: 00:01:28
  Outgoing Interface List
    GigabitEthernet0/0/0/2.201 Flags: F IC NS EX, Up: 00:01:28
```

## Ссылки

[Настройка на IOS XR 5.3.4](https://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k_r5-3/multicast/configuration/guide/b-mcast-cg53x-asr9k/b-mcast-cg53x-asr9k_chapter_0100.html#con_2896297)


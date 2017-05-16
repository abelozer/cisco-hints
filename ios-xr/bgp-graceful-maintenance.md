# BGP Graceful Maintenance

Every time you do maintenance you start with moving traffic out of affected box. There are several methods for link state IGPs:

1. OSPF Stub router
2. ISIS overload bit

There are no techniques for EIGRP and BGP. We'll talk about [BGP Gracefull Mainenance](http://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k_r5-3/general/release/notes/reln-532a9k.html#concept_8267A3B6A4C745B78C6AF4D1C64E9DA1) intruduced in IOS XR 5.3.2.

> **BGP Graceful Maintenance**
>
>When a BGP link or router is taken down, other routers in the network find alternative paths for the traffic that was flowing through the failed router or link, if such alternative paths exist. The time required before all routers involved can reach a consensus about an alternate path is called convergence time. During convergence time, traffic that is directed to the router or link that is down is dropped. The BGP Graceful Maintenance feature allows the network to perform convergence before the router or link is taken out of service. The router or link remains in service while the network reroutes traffic to alternative paths. Any traffic that is yet on its way to the affected router or link is still delivered as before. After all traffic has been rerouted, the router or link can safely be taken out of service.

Let's configure this.

## Initial configuration

![Topology](/img/bgp-gm.png)

### iosv-1 — CE router

```ios
iosv-1#sh ip route ospf
...
      192.168.0.0/32 is subnetted, 2 subnets
O E2     192.168.0.10 [110/1] via 10.1.128.1, 00:13:33, GigabitEthernet0/1
```

### iosxrv-1

By default if you just enable BGP graceful maintenance BGP will set GSHUT community attribute to it's updates. If other routers do not interpret it there will not be any action taken by devices.

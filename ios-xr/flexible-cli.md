# IOS XR Flexible CLI

Flexible CLI uses groups and templates which can be applied to the configuration. The inherited group configuration is not shown with simple **show running-config** but there's a special keyword **inheritance**. If you also add **details** you'll see commented lines _#inherited from group_. This is not very common because you can't see configuration with a command you used to use. Flexible CLI helps to keep configuration standartised and this is a good reason to think about it at least.

## Create groups

First of all we're creating groups. Pay attention to the values in quotes. They represent basic regular expressions for matching some numbers. OSPF process id or if-number.

```cisco
group ospf
 router ospf '[0-9]'
  log adjacency changes
  nsf ietf
  auto-cost reference-bandwidth 100000
  address-family ipv4 unicast
  area '0'
   interface 'Loopback0'
    passive enable
   !
   interface 'Bundle-Ether.*'
    network point-to-point
   !
   interface 'GigabitEthernet.*'
    network point-to-point
   !
  !
 !
end-group
!
group drain
 router ospf '1'
  max-metric router-lsa
 !
end-group
!
group undrain
end-group
!
group interfaces
 cdp
 lldp
 !
 service unsupported-transceiver
 interface 'Gig.*'
  cdp
  mtu 9000
  load-interval 30
 !
end-group
```

## Create templates

Later we'll use these templates as well as groups for dynamic device configuration.

```cisco
template drain (as)
!
route-policy DRAIN-BGP
  prepend as-path $as 5
end-policy
!
end-template
!
template undrain (as)
!
route-policy DRAIN-BGP
end-policy
!
end-template
```

## Apply groups

Apply groups globally.

```cisco
apply-group interfaces ospf undrain
```

At this moment configuration inherits the instructions from three groups.

## Create alias

You may ask why we need groups and templates called _drain_ and _undrain_. I'm going to show how we can use groups for dynamic device configuration. We'll need alias for this.

This alias uses one parameter _action_ for calling group & template name. For simplicity we call them identically.

```cisco
alias setbox (action) apply-group interfaces ospf $action; apply-template $action (1)
```

> **Warning** Apply-group rewrites itself. If you want to add one group more you have to list all of them.

## Use alias

Of course engineers should not do this with their hands. This work is dedicated for scripts.

```cisco
conf
 setbox drain|undrain
commit
```

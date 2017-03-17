# ASR9k Flexible CLI
Flexible CLI uses groups and templates which can be applied to the configuration. The inherited group configuration is not shown with simple **show running-config** but there's a special keyword **inheritance**.

## Create groups

```
group ospf
 router ospf '[0-9]'
  log adjacency changes
  passive enable
  nsf ietf
  auto-cost reference-bandwidth 100000
  address-family ipv4 unicast
  area '0'
   passive enable
   interface 'Loopback0'
    passive enable
   !
   interface 'Bundle-Ether.*'
    network point-to-point
    passive disable
   !
   interface 'GigabitEthernet.*'
    network point-to-point
    passive disable
   !
  !
 !
end-group
group drain
 router ospf '1'
  max-metric router-lsa
 !
end-group
group undrain
end-group
group interface-phy
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
```
!
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

```
apply-group interface-phy ospf undrain
```
At this moment configuration inherits the instructions from three groups.

## Create alias
```
alias setbox (action) apply-group interface-phy ospf $action; apply-template $action (1)
```
## Use alias
Of course engineers should not do this with their hands. THis work is dedicated for scripts.
```
conf
setbox (drain|undrain)
commit
```

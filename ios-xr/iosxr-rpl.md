# Route Policy Language

IOS XR RPL is a powerful replacement of traditional IOS route-maps. One of its main advantages is parametrisation. Parametrisation helps keep configuration unified and prevents from cloning blocks. For example you can create a policy like this:

```bash
route-policy RP-SET-LP($lp)
  set local-preference $lp
  pass
end-policy
```

And attach it to a neighbor with a value for $lp in brackets:

```bash
router bgp 65535
 neighbor 198.51.100.2
  address-family ipv4 unicast
   route-policy RP-SET-LP(300) in
```

Example with using prefix-sets as parameter.

```csharp
prefix-set PS-CUSTOMER-A
     203.0.113.0/24
end-set
!
route-policy RP-PS-PASS($ps)
  if destination in $ps then
    pass
  endif
end-policy
!
router bgp 65535
 neighbor 198.51.100.2
  address-family ipv4 unicast
   route-policy RP-PS-PASS(PS-CUSTOMER-A) in
```


# Route Policy Language

IOS XR RPL is a replacement of traditional IOS route-maps. One of its main advantages is parametrisation. Parametrisation helps keep configuration unified and prevents from cloning blocks. For example you can create a policy like this:

```
route-policy RP-SET-LP($lp)
  set local-preference $lp
  pass
```

And attach it to a neighbor with a value for $lp in brackets:

```
router bgp 65535
 neighbor 198.51.100.2
  route-policy RP-SET-LP(300) in
```

I little bit more complicated example with using prefix-sets.

```
prefix-set PS-CUSTOMER-A
     203.0.113.0/24
end-set
!  
route-policy RP-PS-PASS($ps)
  if destination in $ps then
    pass
  endif
!
router bgp 65535
 neighbor 198.51.100.2
  route-policy RP-PS-PASS(PS-CUSTOMER-A) in
```



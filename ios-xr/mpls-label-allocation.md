# MPLS Label Allocation Template

By default IOS XR allocates labels for all subnets. This is a recommended to allocate labels for /32 only.

## Template

```text
mpls ldp
 address-family ipv4
  label
   local
    allocate for host-routes
   !
  !
 !
```


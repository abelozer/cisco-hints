---
description: >-
  By default IOS XR allocates labels for all subnets. This is a recommended
  configuration to allocate labels to /32 only.
---

# MPLS Label Allocation

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


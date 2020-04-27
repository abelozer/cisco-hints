# BFD on Bundle Member Links

## Configuration Template

```erlang
bfd
 interface Bundle-Ether {{ BUNDLE_ID }}
  echo disable
!
interface Bundle-Ether {{ BUNDLE_ID }}
 [bfd mode {cisco | ietf}]
 bfd address-family ipv4 destination {{ PEER_IP_ADDR }}
 bfd address-family ipv4 fast-detect
 bfd address-family ipv4 minimum-interval {{ BFD_MIN_INT }}
 bfd address-family ipv4 multiplier {{ BFD_MULTIPLIER }}
 [bundle minimum-active links {{ BUNDLE_MIN_LINKS }}]
 [bundle maximum-active links {{ BUNDLE_MAX_LINKS }}]
```


# FAT Pseudowire

## Template

{% hint style="info" %}
`static` keyword should be used for interoperability with Cisco 7600.
{% endhint %}

```text
l2vpn
 load-balancing flow src-dst-ip
 !
 pw-class {{ PW_CLASS_NAME }}
  encapsulation mpls
   control-word
   load-balancing
    flow-label both [static]

 bridge group {{ GROUP_NAME }}
  bridge-domain {{ BD_NAME }}
   !
   vfi {{ VFI_ID }}
     neighbor {{ NEIGHBOR_IPV4_ADDR }} pw-id {{ PW_ID }}
      pw-class {{ PW_CLASS_NAME }}
```

## Verification

Use command `show l2vpn bridge-domain bd-name detail` and look for the following line:`Flow Label flags configured (Tx=1,Rx=1) statically`


# C7600 FAT Pseudowire

## Template

```text
platform platform vfi load-balance-label vlan {{ VLAN_ID }}
```

## Verification

You can verify FAT PW on DFC

1. `attach` 
2. `enable`
3. `sh platform atom ether-vc vlan {{ VLAN_ID }}` 

Look for `fat_pw:(Yes)` in the output.




# Shared Policy Instance

## Template

```text
policy-map {{ POLICY_NAME }}
 class class-default
  police rate 1000000 bps burst 8000 bytes
   conform-action transmit
   exceed-action drop
 !
!
end-policy-map
!

int gi 0/0/0/1.10
 service-policy output {{ POLICY_NAME }} shared-policy-instance {{ SPI_GROUP_NAME }}
int gi 0/0/0/1.20
 service-policy output {{ POLICY_NAME }} shared-policy-instance {{ SPI_GROUP_NAME }}
```


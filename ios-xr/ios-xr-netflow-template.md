# IOS XR Netflow Template

```java
flow exporter-map {{ NF_EXPORTER_MAP_NAME }}
 version v9
 transport udp {{ NF_DST_PORT }}
 source {{ NF_SOURCE_INTERFACE }}
 destination {{ NF_DST_IP }}
!
flow monitor-map {{ NF_MONITOR_MAP_NAME }}
 record ipv4
 exporter {{ NF_EXPORTER_MAP_NAME }}
 cache entries {{ NF_CACHE_ENTRIES }}
!
sampler-map {{ NF_SAMPLER_MAP_NAME }}
 random 1 out-of {{ NF_SAMPLING_INTERVAL }}

interface {{ IFACE }}
 flow ipv4 monitor {{ NF_MONITOR_MAP_NAME }} sampler {{ NF_SAMPLER_MAP_NAME }} ingress
 flow ipv4 monitor {{ NF_MONITOR_MAP_NAME }} sampler {{ NF_SAMPLER_MAP_NAME }} egress
```


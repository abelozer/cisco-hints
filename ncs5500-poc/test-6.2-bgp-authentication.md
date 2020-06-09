# Test 6.2: BGP Authentication

[BGP Authentication](https://www.cisco.com/c/en/us/td/docs/iosxr/ncs5500/bgp/70x/b-bgp-cg-ncs5500-70x/b-bgp-cg-ncs5500-70x_chapter_010.html#concept_a1l_zfv_t1b)

## **BGP authentication configuration template**

```erlang
router bgp {{ AS }}
 neighbor {{ NEIGHBOR_ADDRESS }}
  password clear {{ PASSWORD }}
 !
!
end
```

## **BGP authentication configuration example**

## **BGP authentication verification**


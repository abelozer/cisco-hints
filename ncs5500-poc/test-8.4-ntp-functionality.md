# Test 8.4: NTP functionality

## **NTP configuration example**

```text
ntp
 authentication-key 5 md5 encrypted cisco
 authenticate
 trusted-key 5
 server vrf Mgt 10.1.10.7 key 5
 source Loopback0
 update-calendar
!
```

## **NTP Verification**

```text
RP/0/RP0/CPU0:NCS540-02#sh ntp associations
Mon Mar 30 09:18:25.647 UTC

      address         ref clock     st  when  poll reach  delay  offset    disp
*~10.1.10.7 vrf Mgt
                   192.168.200.9     4   132   128  377    1.59  -0.002   0.006
 * sys_peer, # selected, + candidate, - outlayer, x falseticker, ~ configured
```

```text
RP/0/RP0/CPU0:NCS540-02#sh ntp status
Mon Mar 30 09:18:33.306 UTC

Clock is synchronized, stratum 5, reference is 10.1.10.7
nominal freq is 1000000000.0000 Hz, actual freq is -303831165.7828 Hz, precision is 2**23
reference time is E22C375D.3BD356EC (09:16:13.233 UTC Mon Mar 30 2020)
clock offset is -1.512 msec, root delay is 11.453 msec
root dispersion is 83.90 msec, peer dispersion is 7.39 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is -0.0000042913 s/s
system poll interval is 128, last update was 140 sec ago
authenticate is enabled
```


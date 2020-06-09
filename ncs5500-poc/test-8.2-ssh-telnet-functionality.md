# Test 8.2: SSH/Telnet functionality

{% hint style="info" %}
Password policy
{% endhint %}

```text
aaa password-policy AAA-PASSWORD-HARDENING
 numeric 1
 lower-case 1
 min-length 8
 upper-case 1
 special-char 1
 lockout-time minutes 10
 restrict-username
 restrict-old-count 1
 authen-max-attempts 5
 max-char-repetition 2
```

```erlang
ssh server logging
ssh server disable hmac hmac-sha1
ssh server algorithms cipher aes256-ctr aes128-gcm@openssh.com aes256-gcm@openssh.com
ssh server algorithms host-key ecdsa-nistp256 ecdsa-nistp384 ecdsa-nistp521
ssh server algorithms key-exchange ecdh-sha2-nistp521 ecdh-sha2-nistp384 ecdh-sha2-nistp256
ssh server v2
ssh server vrf {{ VRF_NAME }} ipv4 access-list {{ SSH_ACL }}
ssh server netconf vrf {{ VRF_NAME }} ipv4 access-list {{ NETCONF_ACL }}
```


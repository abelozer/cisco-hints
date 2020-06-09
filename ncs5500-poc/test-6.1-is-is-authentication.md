# Test 6.1: IS-IS Authentication

[IS-IS Authentication](https://www.cisco.com/c/en/us/td/docs/iosxr/ncs5500/routing/70x/b-routing-cg-ncs5500-70x/b-routing-cg-ncs5500-70x_chapter_010.html#con_1276647)

{% hint style="info" %}
Authentication is available to limit the establishment of adjacencies by using the hello-password command, and to limit the exchange of LSPs by using the lsp-password command. The keychain feature allows IS-IS to reference configured keychains.
{% endhint %}

## **IS-IS Authentication configuration template**

```erlang
key chain {{ KEY_CHAIN_NAME }}
 key {{ KEY_ID }}
  accept-lifetime {{ KEY_ACCEPT_LIFETIME }}
  key-string password {{ ISIS_KEY_STRING }}
  send-lifetime {{ KEY_SEND_LIFETIME }}
  cryptographic-algorithm {{ CRYPTO_ALGO }}
 !
 accept-tolerance {{ KEY_TOLERANCE }}
!
```

```erlang
router isis {{ PROCESS }}
 lsp-password keychain {{ KEY_CHAIN_NAME }} level {{ ISIS_LEVEL }}
 !
 interface {{ IFACE }}
  hello-password keychain {{ KEY_CHAIN_NAME }}
```

## **IS-IS Authentication configuration example**

```text
key chain KEY-ISIS
 key 1
  accept-lifetime 12:10:00 june 01 2020 infinite
  key-string password 1124202C243B38272113
  send-lifetime 10:10:00 june 01 2020 infinite
  cryptographic-algorithm HMAC-SHA1-20
 !
 accept-tolerance 600
!
router isis CORE
 lsp-password keychain KEY-ISIS level 2
 !
 interface GigabitEthernet0/0/0/1
  hello-password keychain KEY-ISIS
```

## **IS-IS Authentication verification**


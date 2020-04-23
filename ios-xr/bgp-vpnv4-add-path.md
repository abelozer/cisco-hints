# BGP VPNv4 Add-path

BGP VPNv4 add-path is a unique feature of IOS XR. IOS-XE and NX-OS platforms do not support it.

{% tabs %}
{% tab title="ASBR" %}
{% hint style="warning" %}
`retain route-target all` is critical for ASBRs to accept all route-targets.
{% endhint %}

```erlang
router bgp {{ ASN }}
 address-family vpnv4 unicast
  additional-paths receive
  additional-paths selection route-policy {{ POLICY_NAME }}
  retain route-target all
!
route-policy {{ POLICY_NAME }}
  set path-selection backup 1 install
end-policy
```
{% endtab %}

{% tab title="PE" %}
{% hint style="info" %}
PE routers do not have `retain route-target all` command.
{% endhint %}

```erlang
router bgp {{ ASN }}
 address-family vpnv4 unicast
  additional-paths receive
  additional-paths selection route-policy {{ POLICY_NAME }}
  retain route-target all
!
route-policy {{ POLICY_NAME }}
  set path-selection backup 1 install
end-policy
```
{% endtab %}

{% tab title="IOS XR RR" %}
{% hint style="info" %}
Supported on XR-based route-reflectors only.
{% endhint %}

```erlang
router bgp {{ ASN }}
 address-family vpnv4 unicast
  additional-paths send
  additional-paths selection route-policy {{ POLICY_NAME }}
!
route-policy {{ POLICY_NAME }}
  set path-selection all advertise
end-policy
```
{% endtab %}
{% endtabs %}




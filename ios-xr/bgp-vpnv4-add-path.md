# BGP VPNv4 Add-path

{% tabs %}
{% tab title="ASBR/PE" %}
```text
router bgp {{ ASN }}
 address-family vpnv4 unicast
  additional-paths receive
  additional-paths selection route-policy {{ POLICY_NAME }}
  {# ASBR only: #}
  retain route-target all
!
route-policy {{ POLICY_NAME }}
  set path-selection backup 1 install
end-policy
```
{% endtab %}

{% tab title="RR IOS-XR" %}
```
router bgp {{ ASN }}
 address-family vpnv4 unicast
  additional-paths send
  additional-paths selection route-policy {{ POLICY_NAME }}

route-policy {{ POLICY_NAME }}
  set path-selection all advertise
end-policy
```
{% endtab %}
{% endtabs %}




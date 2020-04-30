# MPLS TE Exclude Address for L2VPN

## Configuration Template

{% hint style="info" %}
This template works for directly connected router only. But same idea applies to any other router in LSP.
{% endhint %}

{% tabs %}
{% tab title="IOS XR" %}
```erlang
explicit-path name {{ PATH_NAME }}
 index {{ INDEX }} exclude-address ipv4 unicast {{ ADJACENT_IPV4_ADDR }}

interface tunnel-te{{ TUNNEL_ID }}
 ipv4 unnumbered Loopback0
 destination {{ TUNNEL_DST }}
 path-option 10 explicit name {{ PATH_NAME }}
 path-option 20 dynamic

l2vpn
 pw-class {{ PW_CLASS_NAME }}
  encapsulation mpls
   preferred-path interface tunnel-te {{ TUNNEL_ID }}
  !
 !
 bridge group {{ GROUP_NAME }}
  bridge-domain {{ BD_NAME }}
   !
   vfi {{ VFI_ID }}
    neighbor {{ NEIGHBOR_IPV4_ADDR }} pw-id {{ PW_ID }}
     pw-class {{ PW_CLASS_NAME }}
    !
   !
  !
 !
!

```
{% endtab %}

{% tab title="IOS" %}
```erlang
pseudowire-class {{ PW_CLASS_NAME }}
 encapsulation mpls
 preferred-path interface Tunnel{{ TUNNEL_ID }}
!
l2 vfi {{ VFI_NAME }} manual
 vpn id {{ VPN_ID }}
 neighbor {{ NEIGHBOR_IPV4_ADDR }} pw-class {{ PW_CLASS_NAME }}
!
ip explicit-path name {{ PATH_NAME }} enable
 index {{ INDEX }} exclude-address {{ ADJACENT_IPV4_ADDR }}
!
interface Tunnel{{ TUNNEL_ID }}
 ip unnumbered Loopback0
 tunnel mode mpls traffic-eng
 tunnel destination {{ TUNNEL_DST }}
 tunnel mpls traffic-eng path-option 10 explicit name {{ PATH_NAME }}
 tunnel mpls traffic-eng path-option 20 dynamic

```
{% endtab %}
{% endtabs %}


# MPLS TE Affinity for L2VPN

The default affinity value for MPLS TE tunnel is 0x0 with mask 0xFFFF. The mask means that 16 bits of a link attribute-flag are compared with tunnel affinity value. At least 16 first bits of attribute flag should be zeros. Each bit which has 1 in the mask is compared between affinity and attribute-flag. Let's look at a default example.

Default Attribute flag: $$0000$$$$0000$$$$0000$$$$0000$$  
Default Affinity value: $$0000$$$$0000$$$$0000$$$$0000$$  
Default Affinity mask: $$1111$$$$1111$$$$1111$$$$1111$$ 

If values of an interface flag match the tunnel affinity values for the bits specified by a mask \(1 in a mask means that algorithm must check the bit\) than tunnel can use this particular link to build LSP.

It's easy to use 17th bit in a link attribute-flags to mark "bad" links. By default tunnels will use links with attribute-flag 0x1ffff because tunnel affinity mask does not require to check 17th bit. The avoiding of "bad" links is configured in path-option with attribute-set. If this path-option fails tunnel will use default affinity and build LSP over 0x10000 marked links.

Applying attribute-flags to interfaces.

{% tabs %}
{% tab title="IOS XR" %}
```erlang
mpls traffic-eng
 interface {{ BAD_NNI_IFACE }}
  description BAD INTERFACE TO AVOID
  attribute-flags 0x10000
```
{% endtab %}

{% tab title="IOS" %}
```erlang
interface {{ BAD_NNI_IFACE }}
 description BAD INTERFACE TO AVOID
 mpls traffic-eng tunnels
 mpls traffic-eng attribute-flags 0x10000
```
{% endtab %}
{% endtabs %}

Following example shows how to use affinity with L2VPNs. L2VPNs will avoid "bad" links by default but will use them if there's no alternative path.

{% tabs %}
{% tab title="IOS XR" %}
```erlang
mpls traffic-eng
 interface {{ BAD_NNI_IFACE }}
  attribute-flags 0x10000
 !
 interface {{ NNI_IFACE }}
 !
 attribute-set path-option {{ ATTR_SET_NAME }}
  affinity 0x0 mask 0x1ffff
 !
!
interface tunnel-te{{ TUNNEL_ID }}
 ipv4 unnumbered Loopback0
 destination {{ TUNNEL_DST }}
 path-option 5 dynamic attribute-set {{ ATTR_SET_NAME }}
 path-option 10 dynamic
!
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
mpls traffic-eng lsp attributes {{ ATTR_SET_NAME }}
 affinity 0x0 mask 0x1FFFF

interface Tunnel{{ TUNNEL_ID }}
 ip unnumbered Loopback0
 tunnel mode mpls traffic-eng
 tunnel destination {{ TUNNEL_DST }}
 tunnel mpls traffic-eng path-option 10 dynamic attributes {{ ATTR_SET_NAME }}
 tunnel mpls traffic-eng path-option 20 dynamic
!
pseudowire-class {{ PW_CLASS_NAME }}
 encapsulation mpls
 preferred-path interface Tunnel{{ TUNNEL_ID }}
!
l2 vfi {{ VFI_NAME }} manual
 vpn id {{ VPN_ID }}
 neighbor {{ NEIGHBOR_IPV4_ADDR }} pw-class {{ PW_CLASS_NAME }}
!
```
{% endtab %}
{% endtabs %}

In this case we have the following.

Attribute flag: $$1$$$$0000$$$$0000$$$$0000$$$$0000$$  
Affinity value: $$0$$$$0000$$$$0000$$$$0000$$$$0000$$  
Affinity mask: $$1$$$$1111$$$$1111$$$$1111$$$$1111$$ 

So now mask asks us to compare 17 bits. Interface attribute flag and tunnel affinity value do not match because 17th bits value is different. Tunnels with default affinity value are not affected by this configuration at all because they are still checking only 16 bits.


# IOS XR object-groups
There's nothing new with object-groups. It was implemented either in IOS and NX-OS. This is just a reminder that you can use it on IOS XR. ACE is represented by network and/or port object-groups, the super-set of ACE.

> From Release 4.3.1, object group is only supported on ASR 9000 Enhanced Ethernet Line Card (aka Typhoon) and newer Tomahawk.

## Configuring network object-group
```cisco
1.    configure
2.    object-group network { ipv4 | ipv6 } object-group-name
3.    description description
4.    host address
5.    address { mask | prefix }
6.    range address address
7.    object-group name
8.    commit
```
As you can see there are several options. You can specify single host, subnet or address range. Also you can use nested object-groups.

## Configuring port object-group
```cisco
1.    configure
2.    object-group port object-group-name
3.    description description
4.    { eq | lt | gt }{ protocol | number }
5.    range range range
6.    object-group name
7.    commit
```
In port object-group you can specify the ports equal to, less than, or greater than the specified port number or protocol. Next option is port range. And last — nested object-group.

> Note that you are not specifying transport protocol (TCP or UDP). This stays at ACE itself.

# IOS XR object-groups
There's nothing new with object-groups. It was implemented either in IOS and NX-OS. This is just a reminder that you can use it on IOS XR. ACE is represented by *network* and/or *port* object-groups, the super-set of ACE.

> From Release 4.3.1, object group is only supported on ASR 9000 Enhanced Ethernet Line Card.

```cisco
1.    **configure**
2.    **object-group network** { **ipv4 | ipv6** } object-group-name
3.    **description** *description*
4.    **host** *address*
5.    *address* { *mask | prefix* }
6.    **range** *address address*
7.    **object-group** *name*
8.    **commit**
```
As you can see there are several options. You can specify single host, subnet or address range. Also you can use nested object-groups.

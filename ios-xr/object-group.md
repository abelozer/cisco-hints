---
description: ASR 9000 ACL with object-groups
---
# IOS XR object-groups

There's nothing new with object-groups. It was implemented either in IOS and NX-OS. This is just a reminder that you can use it on IOS XR. ACE is represented by network and/or port object-groups, the super-set of ACE.

> **Warning** From Release 4.3.1, object group is only supported on ASR 9000 Enhanced Ethernet Line Card (aka Typhoon) and newer Tomahawk. Do not use object-groups on Trident LCs.

Object-groups are convenient when you have to repeat some ACEs many times.

> **Danger** There's a known defect [CSCur28806](https://bst.cloudapps.cisco.com/bugsearch/bug/CSCur28806/). Host ACE in object-group matches _any_. As a workaround use /32 subnet entry. Also SMU is available for many releases. Current recommended IOS XR 5.3.4 has integrated fix.

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

As you can see there are several options. You can specify single host, subnet or address range. Also you can use nested object-groups. But I do not recommend nesting.

> **Warning** Do not use nested object-groups. I believe it will be removed from CCO documentation.

Object-group members have no sequence numbers. And you cannot really edit a member. But you still can delete and add a member.

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

In port object-group you can specify the ports equal to (*eq*), not equal (*neq*), less than (*lt*), or greater than (*gt*) the specified port number or protocol. Next option is port range. And last — not recommended nested object-group.

> **Note** You are not specifying transport protocol (TCP or UDP). This stays at ACE itself.

Now you can use object-groups within ACLs prepended with keywords net-group or port-group.

## Links

1. [R6.0.x](http://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k_r6-0/addr-serv/configuration/guide/b-ipaddr-cg60xasr9k/b-ipaddr-cg60a9k_chapter_010.html#concept_BC3BE6FCEC6D4ED3804ED05DA84FC2FA)
2. [R5.3.x](http://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k_r5-3/addr-serv/configuration/guide/b-ipaddr-cg53asr9k/b-ipaddr-cg53asr9k_chapter_010.html#concept_BC3BE6FCEC6D4ED3804ED05DA84FC2FA)
3. [R5.2.x](http://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k_r5-2/addr-serv/configuration/guide/b-ipaddr-cg52a9k/b-ipaddr-cg52a9k_chapter_011.html#concept_BC3BE6FCEC6D4ED3804ED05DA84FC2FA)
4. [R5.1.x](http://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k_r5-1/addr_serv/configuration/guide/b_ipaddr_cg51xa9k/b_ipaddr_cg51xa9k_chapter_010.html#concept_BC3BE6FCEC6D4ED3804ED05DA84FC2FA)
5. [R4.3.x](http://www.cisco.com/c/en/us/td/docs/routers/asr9000/software/asr9k_r4-3/addr_serv/configuration/guide/b_ipaddr_cg43xa9k/b_ipaddr_cg42a9k_chapter_01.html#concept_BC3BE6FCEC6D4ED3804ED05DA84FC2FA)

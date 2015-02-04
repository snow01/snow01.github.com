---
layout: post
title: Distributed Apps - Enabling multicast communication
date: 2008-08-06 19:13
author: shailendra
comments: true
categories: [Distributed Application, Distributed Application Fail over, Distributed Application Fallback, Distributed Architecute, Mulicast Communication, Tech]
---
Distributed applications are the one that distribute their task on multiple network nodes (machines/servers) and they co-ordinate their activity through network communication. For a network application there can be multiple failure points - network unavailability, process crash etc. Highly available distributed application need to have fail-over and fallback mechanism. For both fail-over and fallback mechanism, automatic discovery and detection of availability/unavailability of a network node becomes really crucial.

Usually communication on multicast address is used to detect a network node. Generally multicast is enabled on most network. If it is not then you need to contact your network administrator. You can try doing a ping on 224.0.0.1 to test the same. If you have multiple interfaces then you can mention interface name in the ping command, for example -

ping 224.0.0.1 -I eth0

It should respond with ping responses from multiple machines on your node.

In many cases you don't want to enable multicast communication on your public interface, but on private interface. For these cases you need to add the network route for the same, here eth0 is private interface for me -

route add -net 224.0.0.0 netmask 224.0.0.0 dev eth0

Happy Multicasting !
<div class="blogger-post-footer"><img alt="" width="1" height="1" /></div>

---
layout: post
title: Firewall your system - IP Tables
date: 2008-08-06 19:03
author: shailendra
comments: true
categories: [Firewall, IPTables, Aside, Tech]
---
<h3>Setting up iptables:</h3>
iptables exist on all linux systems. Generally following command should start the iptables
<i> service iptables start</i>
But most of the time above doesn't work on a fresh system. You can check whether iptables is running or not using: <i>service iptables status</i>

If it prints lots of output or if it prints "Firewall is not configured" then it means iptables is up and running. Else if it prints "Firewall is stopped", then you need to do following jugalary:
<ol style="margin-left:40px;">
	<li><i>cd /etc/sysconfig/</i></li>
	<li><i>touch iptables</i></li>
	<li><i>chmod 600 iptables</i></li>
	<li><i>service iptables start</i></li>
</ol>
Now "<i>service iptables status</i>" command should work as expected.
If you want to start iptables on system restart then do following:
<i>chkconfig iptables on</i>
<i>
</i>
<h3>Setting the IPTABLES Rules:</h3>
Following are the example iptables rules which would just allow traffic at port 80 and 22. Following also disallow any port scanner attempts...

iptables -P INPUT ACCEPT;

iptables -F INPUT;
iptables -F OUTPUT;
iptables -F FORWARD;
iptables -F;

iptables -X;

iptables -N THRU;

iptables -A INPUT -i eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT;

iptables -A INPUT -i lo -j ACCEPT;
iptables -A INPUT -i eth0 -j ACCEPT;

iptables -A INPUT -i eth1 -p tcp -m tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG NONE -j REJECT --reject-with tcp-reset;
iptables -A INPUT -i eth1 -p tcp -m tcp --tcp-flags FIN,SYN FIN,SYN -j REJECT --reject-with tcp-reset;
iptables -A INPUT -i eth1 -p tcp -m tcp --tcp-flags SYN,RST SYN,RST -j REJECT --reject-with tcp-reset;
iptables -A INPUT -i eth1 -p tcp -m tcp --tcp-flags FIN,RST FIN,RST -j REJECT --reject-with tcp-reset;
iptables -A INPUT -i eth1 -p tcp -m tcp --tcp-flags FIN,ACK FIN -j REJECT --reject-with tcp-reset;
iptables -A INPUT -i eth1 -p tcp -m tcp --tcp-flags ACK,URG URG -j REJECT --reject-with tcp-reset;

iptables -A INPUT -j THRU;

iptables -A THRU -p icmp -m limit --limit 1/sec -m icmp --icmp-type 8 -j ACCEPT;
iptables -A THRU -i eth1 -p tcp -m tcp --dport 22 -j ACCEPT;
iptables -A THRU -i eth1 -p tcp -m tcp --dport 80 -j ACCEPT;

iptables -A THRU -p tcp -j REJECT --reject-with tcp-reset;
iptables -A INPUT -p tcp -j REJECT --reject-with tcp-reset;
iptables -A THRU -j DROP;
iptables -A INPUT -j DROP;
iptables -P INPUT DROP;
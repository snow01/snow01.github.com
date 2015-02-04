---
layout: post
title: Manage and Monitor Tomcat Application using jmx
date: 2010-04-19 21:04
author: shailendra
comments: true
categories: [JMX, Management, Profiling, Tech, Tomcat]
---
<strong>1) Add following to CATALINA_OPTS in your tomcat startup script--</strong>

-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=8040 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false

<strong>2) Install JManage application from:</strong> http://www.jmanage.org/

<strong>3) Follow the deployment guide here: </strong>http://www.jmanage.org/wiki/index.php/JManage_Installation

<strong>4) Login to jmanage: </strong>http://localhost:9090/

<strong>5) Add Tomcat application by </strong>
a) Click Add Application
b) Click on JSR160 Application
c) Provide the URL:
<pre>service:jmx:rmi:///jndi/rmi://localhost:8040/jmxrmi</pre>
<strong>6) Add Managed Objects. Possibly you are interested in Memory and Threading only.</strong>
<div class="blogger-post-footer"><img alt="" width="1" height="1" /></div>

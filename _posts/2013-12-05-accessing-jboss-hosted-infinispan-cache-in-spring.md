---
layout: post
title: Accessing JBoss hosted infinispan cache in Spring
date: 2013-12-05 11:14
author: shailendra
comments: true
categories: [cache, cache manager, infinispan, jboss, spring, Tech]
---
<p style="text-align:justify;">It was not well documented how to access infinispan cache that has been defined in JBoss AS container - so it took bit of exploration and going to cores to basics to make it work. This page is to help others and document it.</p>
<strong>Technologies Used:</strong> I have tested the integration on following versions, but it should work on earlier versions too =
<ol>
	<li>JBoss AS 7.1.1.FINAL</li>
	<li>infinispan 5.1.2.FINALL</li>
	<li>spring framework 3.2.3.RELEASE</li>
	<li>maven</li>
</ol>
<p style="text-align:justify;"><strong>Background on JBoss AS 7.1.1 and Infinispan: </strong>JBoss AS 7 defines / hosts infinispan cache in its standalone*.xml file. For standalone mode, JBoss by default provides 4 such configurations: (a) standalone.xml, (b) standalone-full.xml, (c) standalone-full-ha.xml, (d) standalone-ha.xml</p>
A cache is defined as a cache-container :
<pre>&lt;cache-container name="web" aliases="standard-session-cache" default-cache="repl"&gt;
    &lt;transport lock-timeout="60000"/&gt;
    &lt;replicated-cache name="repl" mode="ASYNC" batching="true"&gt;
         &lt;file-store/&gt;
    &lt;/replicated-cache&gt;
    &lt;replicated-cache name="sso" mode="SYNC" batching="true"/&gt;
    &lt;distributed-cache name="dist" mode="ASYNC" batching="true"&gt;
        &lt;file-store/&gt;
    &lt;/distributed-cache&gt;
 &lt;/cache-container&gt;</pre>
<p style="text-align:justify;">You can define more such caches or you can use the already defined one. Please refer to infinispan documentation for more details:</p>
<p style="text-align:justify;"><a href="http://infinispan.org/docs/5.3.x/user_guide/user_guide.html#_infinispan_subsystem_configuration">http://infinispan.org/docs/5.3.x/user_guide/user_guide.html#_infinispan_subsystem_configuration</a></p>
Configurations reside in &lt;jboss&gt;/standalone/configurations directory

You choose which configuration to use by specifying the configuration file with -c option
<pre style="padding-left:30px;">standalone.bat <strong>-c standalone-ha.xml</strong> -b localhost -u 230.0.0.4 -Djboss.server.base.dir=..standalone 
-Djboss.node.name=nodeA -Djboss.socket.binding.port-offset=100</pre>
<p style="text-align:justify;"><strong>Your Spring Project: </strong>This is the project where you want to access infinispan cache. This is the most important section and you need to pay attention to every details - a small error here might lead to setup not working.</p>
<p style="text-align:justify;">1) First off, get the maven dependencies right --</p>

<pre>&lt;dependency&gt;
    &lt;groupId&gt;org.infinispan&lt;/groupId&gt;
    &lt;artifactId&gt;infinispan-core&lt;/artifactId&gt;
    &lt;version&gt;5.1.2.FINAL&lt;/version&gt;
 &lt;/dependency&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.springframework&lt;/groupId&gt;
    &lt;artifactId&gt;spring-context&lt;/artifactId&gt;
    &lt;version&gt;3.2.5.RELEASE&lt;/version&gt;
 &lt;/dependency&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.infinispan&lt;/groupId&gt;
    &lt;artifactId&gt;infinispan-spring&lt;/artifactId&gt;
    &lt;version&gt;5.1.2.FINAL&lt;/version&gt;
 &lt;/dependency&gt;</pre>
<p style="text-align:justify;">2) JBoss AS follows dynamic modules based class loading - as your project is using infinispan which is not a implicit dependency, you must be explicitly declaring the infinispan dependency in MANIFEST.MF file</p>

<blockquote>
<p style="text-align:justify;">To see what all modules are available as implicit dependency refer here:</p>
<p style="text-align:justify;"><a href="https://docs.jboss.org/author/display/AS7/Implicit+module+dependencies+for+deployments">https://docs.jboss.org/author/display/AS7/Implicit+module+dependencies+for+deployments</a></p>
<p style="text-align:justify;">To see more details on explicit dependency in JBoss refer here:</p>
<p style="text-align:justify;"><a href="https://docs.jboss.org/author/display/AS71/Class+Loading+in+AS7">https://docs.jboss.org/author/display/AS71/Class+Loading+in+AS7</a></p>
</blockquote>
<p style="text-align:justify;">You define infinispan dependency by configuring maven-war-plugin</p>

<pre>&lt;build&gt;
    &lt;plugins&gt;
        &lt;plugin&gt;
            &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
            &lt;artifactId&gt;maven-war-plugin&lt;/artifactId&gt;
            &lt;version&gt;2.4&lt;/version&gt;
            &lt;configuration&gt;
                &lt;archive&gt;
                    &lt;manifestEntries&gt;
                        <strong>&lt;Dependencies&gt;org.infinispan&lt;/Dependencies&gt;</strong>
                    &lt;/manifestEntries&gt;
                &lt;/archive&gt;
            &lt;/configuration&gt;
        &lt;/plugin&gt;
    &lt;/plugins&gt;
&lt;/build&gt;</pre>
<p style="text-align:justify;">3) Define Spring CacheManager bean in spring's application config file: I am using <a title="SpringEmbeddedCacheManager" href="org.infinispan.spring.provider.SpringEmbeddedCacheManager">SpringEmbeddedCacheManager</a> - it needs a native cache manager instance in its constructor. I retrieve infinispan cache defined in jboss through a JNDI lookup - I get an instance of org.infinispan.manager.<strong>EmbeddedCacheManager</strong>.</p>

<pre>&lt;beans xmlns="http://www.springframework.org/schema/beans"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:context="http://www.springframework.org/schema/context"
     xmlns:cache="http://www.springframework.org/schema/cache"
     xmlns:jee="http://www.springframework.org/schema/jee"
     xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
     http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
     http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache.xsd
     http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-3.0.xsd"&gt;
    <strong>&lt;cache:annotation-driven/&gt;</strong>
    <strong>&lt;jee:jndi-lookup id="nativeCacheManager" 
        expected-type="org.infinispan.manager.EmbeddedCacheManager" 
        jndi-name="java:jboss/infinispan/container/web"/&gt;</strong>
    <strong>&lt;bean id="cacheManager" 
        class="org.infinispan.spring.provider.SpringEmbeddedCacheManager"&gt;</strong>
<strong>         &lt;constructor-arg&gt;</strong>
<strong>             &lt;ref bean="nativeCacheManager"/&gt;</strong>
<strong>         &lt;/constructor-arg&gt;</strong>
<strong>     &lt;/bean&gt;</strong>
&lt;/beans&gt;</pre>
4) The defined cacheManager bean is a spring cache manager implementation -- you can simply autowire the cache manager as normal spring bean or use it through @Cacheable annotations.

5) Build the war and deploy in jboss to test your application integration with jboss hosted infinispan cache.

P.S: If you face any difficulties, please feel free to put your questions/feedback in the comments.

Thanks !

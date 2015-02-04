---
layout: post
title: Killing linux processes by name or pattern - the AWK way
date: 2008-08-06 19:07
author: shailendra
comments: true
categories: [AWK, Killing a Process, Linux, Process, Tech]
---
<em>You can use following linux scriplet to kill processes by given name or pattern. The script takes the name/pattern as command line argument.</em>

Main tricks are following -
1) Grepping the command by given name/pattern.
2) Avoiding the grep process itself to appear in the list.
3) Use awk to pick the process id field.

#!/bin/sh
_id=$1

for die1 in `ps -ef | grep ^${_id} | grep -v grep | awk '{print $2}'`
do
kill -1 ${die1} &gt;&gt;/dev/null 2&gt;&amp;1
done
sleep 1
for die2 in `ps -ef | grep ^${_id} | grep -v grep | awk '{print $2}'`
do
kill -15 ${die2} &gt;&gt;/dev/null 2&gt;&amp;1
done
sleep 1
for dienow in `ps -ef | grep ^${_id} | grep -v grep | awk '{print $2}'`
do
kill -9 ${dienow} &gt;&gt;/dev/null 2&gt;&amp;1
done

echo "All processes owned by $_id should be dead now"
exit 0
<div class="blogger-post-footer"><img alt="" width="1" height="1" /></div>

---
layout: post
title: Permission denied: user=root, access=WRITE exception in Hadoop
---

Just setup a hadoop cluster and when starting all the services, I was getting below exception

Name node is in safe mode

#Resolution:

Executed following command and it resolved the issue

hadoop dfsadmin -safemode leave

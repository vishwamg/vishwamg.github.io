---
layout: post
title: Name node is in safe mode exception
---

Just setup a hadoop cluster and when starting all the services, I was getting ane exception\n
Name node is in safe mode

Resolution:\n
Executed following command and it resolved the issue
hadoop dfsadmin -safemode leave

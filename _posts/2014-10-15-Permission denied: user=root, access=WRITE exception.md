---
layout: post
title: Permission denied
---

After setting up hadoop cluster when I started first time, I was getting following error

ERROR org.apache.hadoop.security.UserGroupInformation: PriviledgedActionException as:root cause:org.apache.hadoop.security.AccessControlException: Permission denied: user=root, access=WRITE, inode="small":root:supergroup:rwxr-xr-x

#Resolution:

Executed following command and it resolved the issue

hadoop dfsadmin -safemode leave

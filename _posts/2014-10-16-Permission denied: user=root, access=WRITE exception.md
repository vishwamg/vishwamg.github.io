---
layout: post
title: Permission denied
---

After setting up hadoop cluster when I started first time, I was getting following error

ERROR org.apache.hadoop.security.UserGroupInformation: PriviledgedActionException as:root cause:org.apache.hadoop.security.AccessControlException: Permission denied: user=root, access=WRITE, inode="small":root:supergroup:rwxr-xr-x

#Resolution:

The exception was thrown because root user did not had permissions on target direcory "small" which was in hdfs
I had to change permissions for that directory through hadoop command

hadoop fs -mkdir /data/small 

hadoop fs -chown root:root /data/small

if you are running as any other user than root, you might need to use following commands

sudo -su root hadoop fs -mkdir /data/small 

sudo -su root hadoop fs -chown username:grpname /data/small



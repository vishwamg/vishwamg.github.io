---
layout: post
title: Permission denied: user=root, access=WRITE exception in Hadoop
---

After setting up hadoop cluster when I started first time, I was getting following error



#Resolution:

The exception was thrown because root user did not had permissions on target direcory "small" which was in hdfs
I had to change permissions for that directory through hadoop command

hadoop fs -mkdir /data/small 

hadoop fs -chown root:root /data/small

if you are running as any other user than root, you might need to use following commands

sudo -su root hadoop fs -mkdir /data/small 

sudo -su root hadoop fs -chown username:grpname /data/small



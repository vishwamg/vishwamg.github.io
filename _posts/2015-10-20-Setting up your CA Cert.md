---
layout: post
title: Setting up your CA Cert
---


SETTING UP YOUR CA
-----------------------------------

Step 1.  Go to www.openssl.org and download the source code.  Even Windows
users need to build it, so you'll need access to a C compiler.  You may be
able to get hold of prebuilt binaries on the web and you can certainly get
hold of the GNU C compiler or you can use Borland and Microsoft compilers.
There are good build instructions included with the source distribution, so
I won't go into build details.

Step 2.  Create directories to hold your CA keys, your server keys and, if
you want to use SSL client authentication, your client keys.  For the sake
of argument let's assume that these directories are called "ssl/ca",
"ssl/server" and "ssl/client".

Step 3.  Create a private key and certificate request for your own CA:
openssl req -new -newkey rsa:1024 -nodes -out ssl/ca/ca.csr -keyout
ssl/ca/ca.key

Step 4.  Create your CA's self-signed certificate (note lasts one year -
increase the days setting to whatever you want):
openssl x509 -trustout -signkey ssl/ca/ca.key -days 365 -req -in
ssl/ca/ca.csr -out ssl/ca/ca.pem
WINDOWS USERS:If you copy the ca.pem file to ca.crt and edit the file so
that the strings "TRUSTED CERTIFICATE" read "CERTIFICATE", you can import
your CA certificate into your trusted root certificates store.

Step 5.  Import the CA certificate into the JDK certificate authorities
keystore:
keytool -import -keystore $JAVA_JOME/jre/lib/security/cacerts -file
ssl/ca/ca.pem -alias my_ca

Windows users need to replace $JAVA_HOME with %JAVA_HOME%.

Step 6.  Create a file to hold your CA's serial numbers.  This file starts
with the number "2":
echo "02" > ssl/ca/ca.srl

SETTING UP YOUR WEB SERVER
----------------------------------------------------

Step 7.  Create a keystore for your web server.
keytool -genkey -alias tomcat -keyalg RSA -keysize 1024 -keystore
ssl/server/server.ks -storetype JKS

Step 8.  Create a certificate request for your web server.
keytool -certreq -keyalg RSA -alias tomcat -file
ssl/server/server.csr -keystore ssl/server/server.ks
You need to edit the certificate request file slightly.  Open it up in a
text editor and amend the text which reads "NEW CERTIFICATE REQUEST" to
"CERTIFICATE REQUEST"

Step 9.  Have your CA sign your certificate request:
openssl x509 -CA ssl/ca/ca.pem -CAkey ssl/ca/ca.key -CAserial
ssl/ca/ca.srl -req -in ssl/server/server.csr -out
ssl/server/server.crt -days 365

Step 10.  Import your signed server certificate into your server keystore:
keytool -import -alias tomcat -keystore
ssl/server/server.ks -trustcacerts -file ssl/server/server.crt
You should see a message "Certificate reply was installed in keystore".

Step 11.  Import your CA certificate into your server keystore:
keytool -import -alias my_ca -keystore
ssl/server/server.ks -trustcacerts -file ssl/ca/ca.pem
This step is only necessary if you wish to use SSL client authentication
with Tomcat.

Step 12. Set up an SSL connector for Tomcat.  I assume that you know, or can
find out, how to do this.  Open up conf/server.xml in a text editor and
search for the text "keystoreFile".  Ensure that the attribute value is the
keystore you've created above.

SETTING UP AN SSL CLIENT
-------------------------------------------

Step 13.  Create a client certificate request:
openssl req -new -newkey rsa:512 -nodes -out ssl/client/client1.req -keyout
ssl/client/client1.key
The common name of the client must match a user in Tomcat's user realm (e.g.
an entry in conf/tomcat-users.xml).

Step 14.  Have your CA sign your client certificate.
openssl x509 -CA ssl/ca/ca.pem -CAkey ssl/ca/ca.key -CAserial
ssl/ca/ca.srl -req -in ssl/client/client1.req -out
ssl/client/client1.pem -days 365

Step 15.  Generate a PKCS12 file containing your server key and server
certificate.
openssl pkcs12 -export -clcerts -in ssl/client/client1.pem -inkey
ssl/client/client1.key -out ssl/client/client1.p12 -name
"my_client_certificate"

Step 16.  Import the PKCS12 file into your web browser to use as your client
certificate and key.

Repeat steps 13-16 as often as required.

Step 17.  Enable client certificate authentication in Tomcat.  Open up
conf/server.xml and search for the text "clientAuth".  Set the value of the
attribute to "true".

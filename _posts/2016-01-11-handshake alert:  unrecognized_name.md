---
layout: post
title: handshake alert  unrecognized_name
---

Making call to secure url in java code was throwing an exception
javax.net.ssl.SSLProtocolException: handshake alert:  unrecognized_name
	at sun.security.ssl.ClientHandshaker.handshakeAlert(ClientHandshaker.java:1438)
	
Have gone through couple of online resources and found following different solutions

RESOLUTION
-----------

1. System.setProperty("jsse.enableSNIExtension", "false");
But the above solution will impact all applications running on that jvm 
alternaive solution can be
2. try to set host name verifier to true so that jdk will assume that you are sending request to a trusted host

Ex:
	URL url = new URL("some url");
	HttpsURLConnection connection =  (HttpsURLConnection) url.openConnection();        
    connection.setHostnameVerifier(
        	      new HostnameVerifier() {
        	        //public boolean verify(String urlHostname , String certHostname) {return true;}
        	        public boolean verify(String hostname, SSLSession session) {return true;}
        	      }
        	    );

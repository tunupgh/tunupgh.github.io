---
layout: post
title: Adding an SSL certificate on an ASA
categories:
- cisco
- Scripts
- asa
- ca
- certificate
comments: true
---
This tutorial is to show you how to install a HTTPS/SSL certificate on an ASA. This is often used when WebVPN or AnyConnect is configured which uses SSL. Without a certificate installed the users is given warnings and errors about a missing or invalid certificate.

This has nothing to do with authentication. It's simply the https certificate which is needed for a secure communication to be set up.

Suppose you are working for a place called http://company.com and they want to set up their ASA to allows users to VPN into the network. To access the VPN you can either use IPSec or SSL. Suppose their choice was SSL and they want the URL of the ASA to be https://vpn.company.com. This tutorial will help set the HTTPS certificate for that URL.

It is easier for me to use ASDM when dealing with certificates so this tutorial uses ASDM exclusively.
<h3>Step 1 - Create an Identity Certificate</h3>
Under Configuration -&gt; Device Management -&gt; Certificate Management -&gt; Identity Certificates

Click Add.

Give the Trustpoint a Name.

Choose "Add" a new identity certificate

Choose the key pair to use for encryption.

Click "Select" for the certificate subject DN. In this section it is important to make the CN = the URL of the ASA that this certificate will be serving. It doesn't need to have any trailing slashes. So if the URL is "https://example.com/owa" you can simply make the CN "example.com". 

Click "advanced".

Fill in the FQDN field. This should be exactly the same as CN.

Click Add certificate.
<h3>Step 2 - Send the certificate to the CA</h3>
After completing step 1 you will be presented with the option of saving your certificate.

Send this certificate to the CA such as Symantec or Verisign. They will then process it and send you back your public certificate
<h3>Step 3 - Installing your certificate</h3>
Go back to the ASDM: Configuration -&gt; Device Management -&gt; Certificate Management -&gt; Identity Certificates

Click the certificate you made earlier. Then click Install.

Paste in the certificate the CA sent you. Paste in everything including the BEGIN CERTIFICATE and END CERTIFICATE portions but make sure there are no trailing spaces or carriage returns. You do not need any of the intermediate keys, simply the public cert.
<h3>Step 4 - Enabling your certificate on an interface</h3>
Go to Configuration -&gt; Remote Access VPN -&gt; Network (client) access -&gt; AnyConnect Connection Profiles

Click Device Certificate

Choose the certificate you installed as the one to use for when users HTTPS to this device.

That's it! Test the functionality by going to the URL of your ASA by using HTTPS.
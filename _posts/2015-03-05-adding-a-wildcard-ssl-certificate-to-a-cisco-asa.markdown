---
layout: post
title: Adding a wildcard SSL certificate to a Cisco ASA
date: 2015-03-05 22:51
comments: true
categories:
- cisco
- scripts
- certificate
- ssl
---
A wildcard SSL certificate is where the SSL certificate is good for both the root domain and all subdomains. If we had a wildcard certificate for example.com it means the certificate will be valid for vpn.example.como, mail.example.com, blog.example.com, and example.com. This differs from a normal SSL certificate that specifically indicates one or more domain names in the subject field. There is another post that covers how to [install a basic SSL certificate on a Cisco ASA](http://www.tunnelsup.com/adding-an-ssl-certificate-on-an-asa).

In this post we will be covering how to install a wildcard certificate on a Cisco ASA.

### Before we begin

Verify you have the following:

* The certificate private key file (in pem format)
* The wildcard certificate (in pem format)
* The intermediary certificate (in pem format)

For testing we'll use a Cisco ASA that allows a user to go to it using a browser.

### A quick understanding of the types of certificate files

Examine the certs you have. Open them up and look at the contents. Does it start with "BEGIN CERTIFICATE" and end in "END CERTIFICATE"? If so then that certificate is likely in "pem" format. Files that end in the suffix '.pem' or '.key' usually are in this format. We'll need all the certs to be in this format for best results. 

There a ways to convert your certs to pem format but we won't cover that here.

### Create the pkcs12 certificate.

In the ASA we will eventually choose to import a certificate from a PKCS12 format file which has the certificate and private key in it together.

We will be using a linux system to convert the key + certificate into one file. We can use this command to do the conversion:

```
$ openssl pkcs12 -export -inkey privkey.pem  -in cert.pem -name "My Wildcard Cert" -out cert.p12
Enter Export Password:
Verifying - Enter Export Password:
$  ls
cert.pem  cert.p12  privkey.pem
```

Here it will also ask you for a new password to encrypt the file. Remember this because we'll use it later when importing the certificate.

You can view the pem certificate contents by doing the following command:

`openssl x509 -in cert.pem -inform pem -noout -text`

Here you're looking for:

> X509v3 Subject Alternative Name: 
>  DNS:*.example.com, DNS:example.com

This indicates what the URL is allowed to be for the cert to think it's a match. Because this is a wildcard cert we see the *.example.com which will match any of the example.com domains.


### First install the intermediate cert
I am a huge fan of the CLI but for some reason I never feel comfortable doing certificate stuff at the CLI.

Go to the ASDM. Navigate to Configuration > Device Management > Certificate Management > CA Certificates.

Click Add. 

Then copy and paste your intermediate CA certificate in the box. You can copy the whole thing including the "BEGIN" and "END" lines in your certificate.

Click install certificate and this should then install and you should see your CA certificate in the list of certificates. Make sure the expiry date is far into the future (at least greater than your certificate).

If this comes back and says "Certificate install failed" then make sure you're certificate is in pem format when pasting in and make sure it's an intermediary CA cert and not your primary cert.




### Add the certificate to the ASA

In the ASDM. Navigate to Configuration > Device Management > Certificate Management > Identity Certificates.

Click Add.

The ASA refers to certificates as "TrustPoints". You can give it any name you want but remember it because we'll refer to it later.

Choose to "import the identity certificate from a file".

Enter the password you used when you created the pkcs12 file (explained earlier in this post).

Browse to the pkcs12 file you create earlier.

Click Add Certificate.

If all went well then you should see your new certificate added to the list. Verify the date is in the future and also verify the clock on the ASA is correct (show clock).

### Assign the certificate to an interface

In the ASDM. Navigate to Configuration > Device Management > Advanced > SSL Settings.

Here you can see which certificate is going to be placed on which interface.

Select the interface you wish to add the certificate to and either double click or hit edit.

Select the new certificate trustpoint you created earlier.

Click Apply for the popup and then Apply at the bottom of the ASDM screen.

### Verify the certificate

Open a web browser like Firefox and navigate to the URL of your ASA using https. Click the little lock icon in the URL field. Click "more information" then "view certificates". Here you should be able to confirm that the certificate you added to the ASA is the same as what you see in the browser.

If it still looks like the old certificate you used to have in there try opening a different browser. Browsers often cache the certificate. Once the cache times out or the user clears the cache the new certificate will be present.






Import SSL Certificate on Jira

1. Prerequisites
2. Steps Involved
3. Validation
4. Import
5. Extras

#1. Prerequisites

You can either setup SSL from Trusted CA certificate or self-signed CA certificate, Keytool

Keytool is provided by JAVA SDK ~/ jre6/bin

Environment used in this documents :

Linux Server – Redhat
JIRA 5.2.7
JAVA 1.6

#2. Steps Involved
For self signed certificate, Please follow only step a.

For 3rd party Trusted CA, Please follow all the steps in the same order.

Digital Certificate that are issued by trusted 3rd party CAs (Certification Authority) provide verification that your Website does indeed represent your company, thereby verifying your company’s identity. Many CAs simply verify the domain name and issue the certificate, whereas other such as VeriSign verifies the existence of your business, the ownership of your domain name, and your authority to apply for the certificate, providing a higher standard of authentication.

A list of CA’s can be found here. Some of the most well known CAs are:

Verisign
Thawte
CAcert (relatively new CA, providing free CA certificates)
Step a)To generate a CSR and Private Key for Tomcat, perform the following steps:

Using the Java JDK Tool (Recommended JDK 1.4 or higher) , Keytool:  Go into the JDK/bin/ directory (/j2sdk1.4.0/bin/)
Using the java keytool command line utility, the first thing you need to do is create a keystore and generate the key pair. Do this with the following command:

keytool -genkey -keysize 2048 -keyalg RSA -alias [Alias name] -keystore [Keystore Name]

Enter keystore password:  Choose a password and enter it when prompted to do so.

What is your first and last name?

[Unknown]:  www.pothireddy.com (example)What is the name of your organizational unit?

[Unknown]:  Atlassian (example)What is the name of your organization?

[Unknown]: Pothireddy (example)What is the name of your City or Locality?

[Unknown]:  Framingham (example)What is the name of your State or Province?

[Unknown]:  MA (example)What is the two-letter country code for this unit?

[Unknown]:  US (example)Is CN=www.pothireddy.com, OU=Atlassian, O=Pothireddy, L=Framingham, ST=MA, C=US correct?

[no]:  yes

Enter key password for <tomcat>

(RETURN if same as keystore password)

NOTE: Please specify the same password for the keystore and the keyentry or else you will receive the following error message when you restart the jakarta engine: “java.security.UnrecoverableKeyException: Cannot recover key”

NOTE: Save the created keystore for future use, We have to maintain same Alias name and also will use same keystore(once we get certificate from CA)

Step b) Generate a CSR off the newly create keystore and keyentry:
 

keytool -certreq -alias tomcat -keyalg RSA -file certreq.csr -keystore [keystorename]

Enter keystore password (from Above Step).The CSR will be saved to your JDK/bin directory:—–BEGIN NEW CERTIFICATE REQUEST—–and—–END NEW CERTIFICATE REQUEST—–
 

NOTE : The newly generated CSR will need to be a 2048 key length size.  Please use this link https://ssl-tools.verisign.com/checker/  to verify the CSR info is correct as I cannot process the CSR if it has an error in it.
Step c) Submit the CSR in our online Certificate enrollment process and fax the necessary documentation to your CA or follow up with your network team.
 

NOTE: Ask them to provide you with X.509 or PKCS#7 formats, preferably in PKCS#7 format.
Step d) Once you receive your certificates, please save PKCS#7 format in .p7b format and X.509 format in .cer format.
We use only PKCS#7 format and for  X.509 format, please refer special cases under extras.
Import the certificate into the Java keystore using the following keytool command:
keytool -import -alias tomcat -trustcacerts -file cert.p7b  -keystore [keystorename] 
NOTE: alias Name and keystore should be same as in step “a”, or else you would be see errors like Input not an X.509 certificate
Now you are all set with your Java Keystore………..
3. Validation :
When you run the below command on your Java Keystore , you should see Entry type: PrivateKeyEntry,

Certificate chain length: 3 (depending upon CA certs) and Certificate[1] should be 1

$ keytool -list -v -keystore .keystore

Enter keystore password:

 

Keystore type: JKS

Keystore provider: SUN

Your keystore contains 1 entry

Alias name: tomcat

Creation date: Mar 15, 2013

Entry type: PrivateKeyEntry

Certificate chain length: 3

Certificate[1]:

——–

——–

——–

——–

There is no validation step for self signed certification. Your Keystore generated in step a is your final keystore as well,

4. Import :
Either follow these steps  or refer https://confluence.atlassian.com/display/JIRA/Running+JIRA+over+SSL+or+HTTPS

[spothi@pothireddy.com atlassian]$ /cust/app/jira-testing-5.2.7/bin/config.sh
Loading application properties from /cust/app/jira-testing-5.2.7/atlassian-jira/WEB-INF/classes/jira-application.properties
Reading database configuration from /cust/app/jira-data/application-data/dbconfig.xml
No graphics display available; using console.
———————-
JIRA Configurator v1.1
———————-

— Main Menu —
[H] Configure JIRA Home
[D] Database Selection
[W] Web Server (incl. HTTP/HTTPs configuration)
[A] Advanced Settings
[S] Save and Exit
[X] Exit without Saving

Main Menu> W

— Web Server Configuration —
Control Port : 8005
Profile : HTTP only
HTTP Port : 8080

[P] Change the Profile (enable/disable HTTP/HTTPs)
[H] Configure HTTP Port
[X] Return to Main Menu

Web Server> P

To change the web server profile, please select one of the following options. The current profile is: HTTP only.

[1] Disabled
[2] HTTP only
[3] HTTP and HTTPs (redirect HTTP to HTTPs)
[4] HTTPs only
Profile (leave blank to exit)> 3
Using currently configured HTTP port: 8080

The next steps gather all required information to set up the HTTPs port (HTTP over SSL encryption). First of all, you need provide a so called key store containing the private key and the signed certificate. This can be either self-signed or obtained from a certified authority (CA). For more information, please see the link below. In order to verify the entered information, this tool will access the key store and print the certificate found.

https://confluence.atlassian.com/display/JIRA/Running+JIRA+over+SSL+or+HTTPS

Please select the keystore from the options below. It must contain the certificate and the private key to be used.
[S] The system-wide Java keystore ($JAVA_HOME/jre/lib/security/cacerts)
[U] User-defined location
Keystore> U
Keystore Path (leave blank to exit)> /home/spothi/jira-pothireddy-com.jks
Keystore Password>
Key Alias> tomcat

The following certificate was found:

SerialNumber: 1195337946531432116328876652202648262647
IssuerDN: CN=Thawte SSL CA, O=”Thawte, Inc.”, C=US
Start Date: Thursday, March 14, 2013
Final Date: Thursday, February 26, 2015
SubjectDN: CN=jira.pothireddy.com, OU=Atlassian, O=Pothireddy, L=Framingham, ST=MA, C=US

Do you want to use this certificate? ([Y]/N)? > Y
HTTPs Port> 6443

Updated the profile to ‘HTTP and HTTPs (redirect HTTP to HTTPs)’. Remember to save the changes on exit.

— Web Server Configuration —
Control Port : 8005
Profile : HTTP and HTTPs (redirect HTTP to HTTPs)
HTTP Port : 8080
HTTPs Port : 6443
Keystore Path : /home/spothi/jira-pothireddy-com.jks
Key Alias : tomcat

[P] Change the Profile (enable/disable HTTP/HTTPs)
[H] Configure HTTP Port
[S] Configure SSL Encryption (requires an installed X509 certificate)
[X] Return to Main Menu

Web Server> x

— Main Menu —
[H] Configure JIRA Home
[D] Database Selection
[W] Web Server (incl. HTTP/HTTPs configuration)
[A] Advanced Settings
[S] Save and Exit
[X] Exit without Saving

Main Menu> S
Storing database configuration in /cust/app/jira-data/application-data/dbconfig.xml
Settings saved successfully.
[spothi@pothireddy.com atlassian]$

 

Restart and you all good to go, by doing this all calls to  HTTP(8080) will also be redirected to HTTPS port(6443 in our case).

5. Extras
SSL Converter to convert SSL certificates to and from different formats such as pem, der, p7b, and pfx

https://www.sslshopper.com/ssl-converter.html

Most Common Keytool Commands :

 

Java Keytool Commands for Creating and Importing
These commands allow you to generate a new Java Keytool keystore file, create a CSR, and import certificates. Any root or intermediate certificates will need to be imported before importing the primary certificate for your domain.

Generate a Java keystore and key pairkeytool -genkey -alias mydomain -keyalg RSA -keystore keystore.jks -keysize 2048
Generate a certificate signing request (CSR) for an existing Java keystorekeytool -certreq -alias mydomain -keystore keystore.jks -file mydomain.csr
Import a root or intermediate CA certificate to an existing Java keystorekeytool -import -trustcacerts -alias root -file Thawte.crt -keystore keystore.jks
Import a signed primary certificate to an existing Java keystorekeytool -import -trustcacerts -alias mydomain -file mydomain.crt -keystore keystore.jks
Generate a keystore and self-signed certificate (see How to Create a Self Signed Certificate using Java Keytool for more info)keytool -genkey -keyalg RSA -alias selfsigned -keystore keystore.jks -storepass password -validity 360 -keysize 2048
Java Keytool Commands for Checking
If you need to check the information within a certificate, or Java keystore, use these commands.

Check a stand-alone certificatekeytool -printcert -v -file mydomain.crt
Check which certificates are in a Java keystorekeytool -list -v -keystore keystore.jks
Check a particular keystore entry using an aliaskeytool -list -v -keystore keystore.jks -alias mydomain
Other Java Keytool Commands
Delete a certificate from a Java Keytool keystorekeytool -delete -alias mydomain -keystore keystore.jks
Change a Java keystore passwordkeytool -storepasswd -new new_storepass -keystore keystore.jks
Export a certificate from a keystorekeytool -export -alias mydomain -file mydomain.crt -keystore keystore.jks
List Trusted CA Certskeytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts
Import New CA into Trusted Certskeytool -import -trustcacerts -file /path/to/ca/ca.pem -alias CA_ALIAS -keystore $JAVA_HOME/jre/lib/security/cacerts
 

Special Cases :

1)  If you lost your initial keystore, the very first keystore which you have generated before submitting it to Trusted CA :

There is no way that you can regenerate the keystore, best possible solution create that keystore and csr file (i.e. step 1 and step b) , then now you have to submit your csr under REVOKE AND REPLACE method.

2) If your CA provides the certificate only in X509 format :

Import the Primary Intermediate certificate (e.g., use alias: primary)
keytool -import -alias primary -trustcacerts -file primary_intermediate_file_name  -keystore [keystorename]
Import the Secondary Intermediate certificate (e.g., use alias: secondary)
keytool -import -alias secondary -trustcacerts -file secondary_intermediate_file_name  -keystore [keystorename]
Import the SSL certificate (Use the same alias name based on the created keystore and submitted CSR from Certified CA)
keytool -import -alias [your_alias_name] -trustcacerts -file X.509_file_name  -keystore [keystorename]
Note : For primary and secondary certificates, contact your trusted CA or you can search online.

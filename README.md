		<header class="entry-header">
			<h1 class="entry-title">Import SSL Certificate on Jira</h1>
		</header><!-- .entry-header -->

		<div class="entry-content">
			<p>&nbsp;</p>
<p><strong>1. Prerequisites<br />
2. Steps Involved<br />
3. Validation<br />
4. Import<br />
</strong><strong>5. Extras</strong></p>
<h2>1. Prerequisites</h2>
<p>You can either setup SSL from Trusted CA certificate or self-signed CA certificate, Keytool</p>
<p>Keytool is provided by JAVA SDK ~/ jre6/bin</p>
<p>Environment used in this documents :</p>
<p>Linux Server &#8211; Redhat<br />
JIRA 5.2.7<br />
JAVA 1.6</p>
<h2>2. Steps Involved</h2>
<p>For self signed certificate, Please follow only step a.</p>
<p>For 3rd party Trusted CA, Please follow all the steps in the same order.</p>
<p>Digital Certificate that are issued by trusted 3rd party CAs (Certification Authority) provide verification that your Website does indeed represent your company, thereby verifying your company&#8217;s identity. Many CAs simply verify the domain name and issue the certificate, whereas other such as <a href="http://www.verisign.com/" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.verisign.com']);" rel="nofollow">VeriSign</a> verifies the existence of your business, the ownership of your domain name, and your authority to apply for the certificate, providing a higher standard of authentication.</p>
<p>A list of CA&#8217;s can be found <a href="http://www.dmoz.org/Computers/Security/Public_Key_Infrastructure/PKIX/Tools_and_Services/Third_Party_Certificate_Authorities/" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.dmoz.org']);" rel="nofollow">here</a>. Some of the most well known CAs are:</p>
<ul>
<li><a href="http://www.verisign.com/" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.verisign.com']);" rel="nofollow">Verisign</a></li>
<li><a href="http://www.thawte.com/" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.thawte.com']);" rel="nofollow">Thawte</a></li>
<li><a href="http://www.cacert.org/" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.cacert.org']);" rel="nofollow">CAcert</a> (relatively new CA, providing free CA certificates)</li>
</ul>
<p><strong>Step a)To generate a CSR and Private Key for Tomcat, perform the following steps:</strong></p>
<div>Using the Java JDK Tool (<strong>Recommended JDK 1.4 or higher</strong>) , Keytool:  Go into the <strong>JDK/bin/ directory (/j2sdk1.4.0/bin/)</strong></div>
<div></div>
<div>
<p>Using the java keytool command line utility, the first thing you need to do is create a keystore and generate the key pair. Do this with the following command:</p>
<div></div>
<div><strong>keytool -genkey -keysize 2048 -keyalg RSA -alias [Alias name] -keystore [Keystore Name]</strong></div>
<div></div>
<div>Enter keystore password:  Choose a password and enter it when prompted to do so.</div>
<div></div>
<div>What is your first and last name?<br />
[Unknown]:  www.pothireddy.com (example)What is the name of your organizational unit?<br />
[Unknown]:  Atlassian (example)What is the name of your organization?<br />
[Unknown]: Pothireddy (example)What is the name of your City or Locality?<br />
[Unknown]:  Framingham (example)What is the name of your State or Province?<br />
[Unknown]:  MA (example)What is the two-letter country code for this unit?<br />
[Unknown]:  US (example)Is CN=www.pothireddy.com, OU=Atlassian, O=Pothireddy, L=Framingham, ST=MA, C=US correct?<br />
[no]:  yes</div>
<div></div>
<div>Enter key password for &lt;tomcat&gt;</div>
<div>(RETURN if same as keystore password)</div>
</div>
<div></div>
<div>
<p><strong>NOTE</strong>: <em>Please specify the same password for the keystore and the keyentry or else you will receive the following error message when you restart the jakarta engine: &#8220;java.security.UnrecoverableKeyException: Cannot recover key&#8221;</em></p>
<p><strong>NOTE:</strong> <em>Save the created keystore for future use, We have to maintain same Alias name and also will use same keystore(once we get certificate from CA)</em></p>
</div>
<div></div>
<div><strong>Step b) Generate a CSR off the newly create keystore and keyentry:</strong></div>
<p>&nbsp;</p>
<div><strong>keytool -certreq -alias tomcat -keyalg RSA -file certreq.csr -keystore [keystorename]</strong></div>
<div>
<div></div>
<div>Enter keystore password (from Above Step).The CSR will be saved to your JDK/bin directory:&#8212;&#8211;BEGIN NEW CERTIFICATE REQUEST&#8212;&#8211;and&#8212;&#8211;END NEW CERTIFICATE REQUEST&#8212;&#8211;</div>
<div></div>
<p>&nbsp;</p>
<div><strong>NOTE</strong> : The newly generated CSR will need to be a 2048 key length size.  Please use this link <a href="https://ssl-tools.verisign.com/checker/" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://ssl-tools.verisign.com']);">https://ssl-tools.verisign.com/checker/</a>  to verify the CSR info is correct as I cannot process the CSR if it has an error in it.</div>
<div></div>
<div><strong>Step c) Submit the CSR in our online Certificate enrollment process and fax the necessary documentation to your CA or follow up with your network team.</strong></div>
</div>
<p>&nbsp;</p>
<div></div>
<div><strong>NOTE</strong>: Ask them to provide you with X.509 or PKCS#7 formats, preferably in PKCS#7 format.</div>
<div></div>
<div><strong>Step d) Once you receive your certificates, please save PKCS#7 format in .p7b format and X.509 format in .cer format.</strong></div>
<div></div>
<div>We use only <strong>PKCS#7 format</strong> and for <strong> X.509 format</strong>, please refer special cases under extras.</div>
<div></div>
<div>Import the certificate into the Java keystore using the following keytool command:<br />
<strong>keytool -import -alias tomcat -trustcacerts -file cert.p7b  -keystore [keystorename] </strong></div>
<div><strong>NOTE</strong>: <strong><em>alias Name and keystore should be same as in step &#8220;a&#8221;</em></strong>, or else you would be see errors like <span style="color: #000000; font-weight: bold;">Input not an X.509 certificate</span></div>
<div></div>
<div>Now you are all set with your Java Keystore&#8230;&#8230;&#8230;..</div>
<div></div>
<div></div>
<div>
<h2>3. Validation :</h2>
</div>
<div>
<p>When you run the below command on your Java Keystore , you should see Entry type: <strong>PrivateKeyEntry,</strong></p>
<p><strong>Certificate chain length: 3 (depending upon CA certs) and </strong><strong>Certificate[1] should be 1</strong></p>
<p>$ keytool -list -v -keystore .keystore</p>
<p>Enter keystore password:</p>
<p>&nbsp;</p>
<p>Keystore type: JKS</p>
<p>Keystore provider: SUN</p>
<p>Your keystore contains <em>1 entry</em></p>
<p>Alias name: tomcat</p>
<p>Creation date: Mar 15, 2013</p>
<p>Entry type: <strong>PrivateKeyEntry</strong></p>
<p><strong>Certificate chain length: 3</strong></p>
<p><strong>Certificate[1]:</strong></p>
<p>&#8212;&#8212;&#8211;</p>
<p>&#8212;&#8212;&#8211;</p>
<p>&#8212;&#8212;&#8211;</p>
<p>&#8212;&#8212;&#8211;</p>
<p>There is no validation step for <strong>self signed certification</strong>. Your Keystore generated in step a is your final keystore as well,</p>
<h2>4. Import :</h2>
<p>Either follow these steps  or refer https://confluence.atlassian.com/display/JIRA/Running+JIRA+over+SSL+or+HTTPS</p>
<p>[spothi@pothireddy.com atlassian]$ <strong>/cust/app/jira-testing-5.2.7/bin/config.sh</strong><br />
Loading application properties from /cust/app/jira-testing-5.2.7/atlassian-jira/WEB-INF/classes/jira-application.properties<br />
Reading database configuration from /cust/app/jira-data/application-data/dbconfig.xml<br />
No graphics display available; using console.<br />
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-<br />
JIRA Configurator v1.1<br />
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-</p>
<p>&#8212; Main Menu &#8212;<br />
[H] Configure JIRA Home<br />
[D] Database Selection<br />
[W] Web Server (incl. HTTP/HTTPs configuration)<br />
[A] Advanced Settings<br />
[S] Save and Exit<br />
[X] Exit without Saving</p>
<p>Main Menu&gt; <strong>W</strong></p>
<p>&#8212; Web Server Configuration &#8212;<br />
Control Port : 8005<br />
Profile : HTTP only<br />
HTTP Port : 8080</p>
<p>[P] Change the Profile (enable/disable HTTP/HTTPs)<br />
[H] Configure HTTP Port<br />
[X] Return to Main Menu</p>
<p>Web Server&gt; <strong>P</strong></p>
<p>To change the web server profile, please select one of the following options. The current profile is: HTTP only.</p>
<p>[1] Disabled<br />
[2] HTTP only<br />
[3] HTTP and HTTPs (redirect HTTP to HTTPs)<br />
[4] HTTPs only<br />
Profile (leave blank to exit)&gt; <strong>3</strong><br />
Using currently configured HTTP port: 8080</p>
<p>The next steps gather all required information to set up the HTTPs port (HTTP over SSL encryption). First of all, you need provide a so called key store containing the private key and the signed certificate. This can be either self-signed or obtained from a certified authority (CA). For more information, please see the link below. In order to verify the entered information, this tool will access the key store and print the certificate found.</p>
<p>https://confluence.atlassian.com/display/JIRA/Running+JIRA+over+SSL+or+HTTPS</p>
<p>Please select the keystore from the options below. It must contain the certificate and the private key to be used.<br />
[S] The system-wide Java keystore ($JAVA_HOME/jre/lib/security/cacerts)<br />
[U] User-defined location<br />
Keystore&gt; <strong>U</strong><br />
Keystore Path (leave blank to exit)&gt;<strong> /home/spothi/jira-pothireddy-com.jks</strong><br />
Keystore Password&gt;<br />
Key Alias&gt; <strong>tomcat</strong></p>
<p>The following certificate was found:</p>
<p>SerialNumber: 1195337946531432116328876652202648262647<br />
IssuerDN: CN=Thawte SSL CA, O=&#8221;Thawte, Inc.&#8221;, C=US<br />
Start Date: Thursday, March 14, 2013<br />
Final Date: Thursday, February 26, 2015<br />
SubjectDN: CN=jira.pothireddy.com, OU=Atlassian, O=Pothireddy, L=Framingham, ST=MA, C=US</p>
<p>Do you want to use this certificate? ([Y]/N)? &gt; <strong>Y</strong><br />
HTTPs Port&gt; <strong>6443</strong></p>
<p>Updated the profile to &#8216;HTTP and HTTPs (redirect HTTP to HTTPs)&#8217;. Remember to save the changes on exit.</p>
<p>&#8212; Web Server Configuration &#8212;<br />
Control Port : 8005<br />
Profile : HTTP and HTTPs (redirect HTTP to HTTPs)<br />
HTTP Port : 8080<br />
HTTPs Port : 6443<br />
Keystore Path : /home/spothi/jira-pothireddy-com.jks<br />
Key Alias : tomcat</p>
<p>[P] Change the Profile (enable/disable HTTP/HTTPs)<br />
[H] Configure HTTP Port<br />
[S] Configure SSL Encryption (requires an installed X509 certificate)<br />
[X] Return to Main Menu</p>
<p>Web Server&gt; <strong>x</strong></p>
<p>&#8212; Main Menu &#8212;<br />
[H] Configure JIRA Home<br />
[D] Database Selection<br />
[W] Web Server (incl. HTTP/HTTPs configuration)<br />
[A] Advanced Settings<br />
[S] Save and Exit<br />
[X] Exit without Saving</p>
<p>Main Menu&gt; <strong>S</strong><br />
Storing database configuration in /cust/app/jira-data/application-data/dbconfig.xml<br />
Settings saved successfully.<br />
[spothi@pothireddy.com atlassian]$</p>
<p>&nbsp;</p>
<p>Restart and you all good to go, by doing this all calls to  HTTP(8080) will also be redirected to HTTPS port(6443 in our case).</p>
<h2>5. Extras</h2>
<blockquote><p><strong>SSL Converter to convert SSL certificates</strong> to and from different formats such as <strong>pem, der, p7b, and pfx</strong></p></blockquote>
<p>https://www.sslshopper.com/ssl-converter.html</p>
<blockquote><p><strong>Most Common Keytool Commands :</strong></p>
<p>&nbsp;</p></blockquote>
<h2>Java Keytool Commands for Creating and Importing</h2>
<p>These commands allow you to generate a new Java Keytool keystore file, create a CSR, and import certificates. Any root or intermediate certificates will need to be imported before importing the primary certificate for your domain.</p>
<ul>
<li><strong>Generate a Java keystore and key pair</strong>keytool -genkey -alias <span style="text-decoration: underline;">mydomain</span> -keyalg RSA -keystore <span style="text-decoration: underline;">keystore.jks </span>-keysize 2048</li>
<li><strong>Generate a certificate signing request (CSR) for an existing Java keystore</strong>keytool -certreq -alias <span style="text-decoration: underline;">mydomain</span> -keystore <span style="text-decoration: underline;">keystore.jks</span> -file <span style="text-decoration: underline;">mydomain.csr</span></li>
<li><strong>Import a root or intermediate CA certificate to an existing Java keystore</strong>keytool -import -trustcacerts -alias root -file <span style="text-decoration: underline;">Thawte.crt</span> -keystore <span style="text-decoration: underline;">keystore.jks</span></li>
<li><strong>Import a signed primary certificate to an existing Java keystore</strong>keytool -import -trustcacerts -alias <span style="text-decoration: underline;">mydomain</span> -file <span style="text-decoration: underline;">mydomain.crt</span> -keystore <span style="text-decoration: underline;">keystore.jks</span></li>
<li><strong>Generate a keystore and self-signed certificate</strong> (see <a href="http://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-using-java-keytool.html" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.sslshopper.com']);">How to Create a Self Signed Certificate using Java Keytool</a> for more info)keytool -genkey -keyalg RSA -alias selfsigned -keystore <span style="text-decoration: underline;">keystore.jks</span> -storepass <span style="text-decoration: underline;">password</span> -validity 360 -keysize 2048</li>
</ul>
<h4>Java Keytool Commands for Checking</h4>
<p>If you need to check the information within a certificate, or Java keystore, use these commands.</p>
<ul>
<li><strong>Check a stand-alone certificate</strong>keytool -printcert -v -file <span style="text-decoration: underline;">mydomain.crt</span></li>
<li><strong>Check which certificates are in a Java keystore</strong>keytool -list -v -keystore <span style="text-decoration: underline;">keystore.jks</span></li>
<li><strong>Check a particular keystore entry using an alias</strong>keytool -list -v -keystore <span style="text-decoration: underline;">keystore.jks</span> -alias mydomain</li>
</ul>
<h4>Other Java Keytool Commands</h4>
<ul>
<li><strong>Delete a certificate from a Java Keytool keystore</strong>keytool -delete -alias <span style="text-decoration: underline;">mydomain</span> -keystore <span style="text-decoration: underline;">keystore.jks</span></li>
<li><strong>Change a Java keystore password</strong>keytool -storepasswd -new new_storepass -keystore <span style="text-decoration: underline;">keystore.jks</span></li>
<li><strong>Export a certificate from a keystore</strong>keytool -export -alias <span style="text-decoration: underline;">mydomain</span> -file <span style="text-decoration: underline;">mydomain.crt</span> -keystore <span style="text-decoration: underline;">keystore.jks</span></li>
<li><strong>List Trusted CA Certs</strong>keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts</li>
<li><strong>Import New CA into Trusted Certs</strong>keytool -import -trustcacerts -file <span style="text-decoration: underline;">/path/to/ca/ca.pem</span> -alias <span style="text-decoration: underline;">CA_ALIAS</span> -keystore $JAVA_HOME/jre/lib/security/cacerts</li>
</ul>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<blockquote><p><strong>Special Cases</strong> :</p></blockquote>
<p>1)  If you lost your initial keystore, the very first keystore which you have generated before submitting it to Trusted CA :</p>
<p>There is no way that you can regenerate the keystore, best possible solution create that keystore and csr file (i.e. step 1 and step b) , then now you have to submit your csr under <em><strong>REVOKE AND REPLACE</strong> </em>method.</p>
<p>2) If your CA provides the certificate only in X509 format :</p>
<ol>
<li>Import the Primary Intermediate certificate (e.g., use alias: primary)<br />
<strong>keytool -import -alias primary -trustcacerts -file primary_intermediate_file_name  -keystore [keystorename]</strong></li>
<li>Import the Secondary Intermediate certificate (e.g., use alias: secondary)<br />
<strong>keytool -import -alias secondary -trustcacerts -file secondary_intermediate_file_name  -keystore [keystorename]</strong></li>
<li>Import the SSL certificate (Use the same alias name based on the created keystore and submitted CSR from Certified CA)<br />
<strong>keytool -import -alias [your_alias_name] -trustcacerts -file X.509_file_name  -keystore [keystorename]</strong></li>
</ol>
</div>
<div><strong>Note</strong> : For primary and secondary certificates, contact your trusted CA or you can search online.</div>
								</div><!-- .entry-content -->
		
	</article><!-- #post-606 -->

					

	<div id="comments" class="comments-area">

	
	
	
									<div id="respond">
				<h3 id="reply-title">Leave a Reply <small><a rel="nofollow" id="cancel-comment-reply-link" href="/knowledge/issue-tracking-tools/jira/import-ssl-certificate-on-jira/#respond" style="display:none;">Cancel reply</a></small></h3>
									<form action="http://www.pothireddy.com/wp-comments-post.php" method="post" id="commentform">
																			<p class="comment-notes">Your email address will not be published. Required fields are marked <span class="required">*</span></p>							<p class="comment-form-author"><label for="author">Name</label> <span class="required">*</span><input id="author" name="author" type="text" value="" size="30" aria-required='true' /></p>
<p class="comment-form-email"><label for="email">Email</label> <span class="required">*</span><input id="email" name="email" type="text" value="" size="30" aria-required='true' /></p>
<p class="comment-form-url"><label for="url">Website</label><input id="url" name="url" type="text" value="" size="30" /></p>
												<p class="comment-form-comment"><label for="comment">Comment</label><textarea id="comment" name="comment" cols="45" rows="8" aria-required="true"></textarea></p>						<p class="form-allowed-tags">You may use these <abbr title="HyperText Markup Language">HTML</abbr> tags and attributes:  <code>&lt;a href=&quot;&quot; title=&quot;&quot;&gt; &lt;abbr title=&quot;&quot;&gt; &lt;acronym title=&quot;&quot;&gt; &lt;b&gt; &lt;blockquote cite=&quot;&quot;&gt; &lt;cite&gt; &lt;code&gt; &lt;del datetime=&quot;&quot;&gt; &lt;em&gt; &lt;i&gt; &lt;q cite=&quot;&quot;&gt; &lt;strike&gt; &lt;strong&gt; </code></p>						<p class="form-submit">
							<input name="submit" type="submit" id="submit" value="Send your comment" />
							<input type='hidden' name='comment_post_ID' value='606' id='comment_post_ID' />
<input type='hidden' name='comment_parent' id='comment_parent' value='0' />
						</p>
						<p style="display: none;"><input type="hidden" id="akismet_comment_nonce" name="akismet_comment_nonce" value="a5c18bcd53" /></p>					</form>
							</div><!-- #respond -->

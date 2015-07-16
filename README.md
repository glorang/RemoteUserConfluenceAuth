# RemoteUserConfluenceAuth
Confluence Plugin to do SSO authentication via Kerberos and Apache's auth_kerb module.

This is completely based on RemoteUserJiraAuth by Angus Warren. 

# Compilation

- Dowload and install Java & Atalassian SDK
- git clone this repo
- run atlas-run, jar file will be in target/ 

# Installation

- Add AJP connector in /opt/atlassian/confluence/conf/server.xml
```
    <Connector port="8009" redirectPort="8443" enableLookups="false" protocol="AJP/1.3" URIEncoding="UTF-8" tomcatAuthentication="false"/> 
```
- Enable Apache module proxy_ajp and auth_kerb
- Configure Apache
```
    <Virtualhost *:443>
     
            Servername ...
     
            ProxyRequests       Off
            ProxyPreserveHost On
            ProxyPass            /       ajp://127.0.0.1:8009/
            ProxyPassReverse     /       ajp://127.0.0.1:8009/
     
            SSLProxyEngine on
            SSLEngine On
            SSLCertificateFile ...
            SSLCertificateKeyFile  ...
            SSLCaCertificateFile ...
     
            <Location />
                    AuthName "Kerberos SSO"
                    AuthType Kerberos
                    KrbAuthRealms ...
                    Krb5Keytab ...
                    KrbServiceName HTTP/...
                    KrbLocalUserMapping on
                    KrbMethodNegotiate on
                    KrbMethodK5Passwd on
                    Require valid-user
            </Location>
     
            <LocationMatch "^/(rest|sr|images/|s/.*_/images/|secure/useravatar).*$">
                    AuthType None
                    Satisfy Any
            </LocationMatch>
     
    </Virtualhost>
     
    <Proxy *>
      Order deny,allow
      Allow from all
    </Proxy>
```
- Create keytab file 
- Setup User Directory in Confluence
- Upload RemoteUserConfluenceAuth-1.0.jar to /opt/atlassian/confluence/WEB-INF/lib/RemoteUserConfluenceAuth-1.0.jar
- Edit /opt/atlassian/confluence/confluence/WEB-INF/classes/seraph-config.xml
- Comment out default Authenticator
```
    <!-- <authenticator class="com.atlassian.confluence.user.ConfluenceAuthenticator"/> -->
```
- Add new authenticator
```
    <authenticator class="glorang.confluence.RemoteUserConfluenceAuth"/>
```
- Restart Apache & Confluence

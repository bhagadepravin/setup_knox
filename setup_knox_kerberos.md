## Apache Knox Kerberos enablement:

https://github.com/apache/knox/blob/master/gateway-release/home/conf/gateway-site.xml

```
cat /etc/knox/conf/krb5JAASLogin.conf
com.sun.security.jgss.initiate {
com.sun.security.auth.module.Krb5LoginModule required
renewTGT=false
doNotPrompt=true
useKeyTab=true
keyTab="/etc/security/keytabs/knox.service.keytab"
principal="knox/$KNOX_HOSTNAME@$REALM"
storeKey=true
useTicketCache=false;
```

```
# Make sure below configs are added to gateway-site.xml  to kerberos enablement. Make sure above file is created with proper permission along with Kerbeors principal and Keytab file present on Knox node.

---> gateway-site.xml <-----
    <property>
        <name>gateway.hadoop.kerberos.secured</name>
        <value>true</value>
        <description>Boolean flag indicating whether the Hadoop cluster protected by Gateway is secured with Kerberos</description>
    </property>

    <property>
        <name>java.security.krb5.conf</name>
        <value>/etc/knox/conf/krb5.conf</value>
        <description>Absolute path to krb5.conf file</description>
    </property>

    <property>
        <name>java.security.auth.login.config</name>
        <value>/etc/knox/conf/krb5JAASLogin.conf</value>
        <description>Absolute path to JAAS login config file</description>
    </property>
    
    <property>
        <name>sun.security.krb5.debug</name>
        <value>false</value>
        <description>Boolean flag indicating whether to enable debug messages for krb5 authentication</description>
    </property>
```

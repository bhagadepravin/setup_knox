# [Service Test API ](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Service+Test+API)



The gateway supports a Service Test API that can be used to test Knox’s ability to connect to each of the different Hadoop services via a simple HTTP GET request. To be able to access this API, one must add the following lines into the topology for which you wish to run the service test.
```
<service>
  <role>SERVICE-TEST</role>
</service>
```
After adding the above to a topology, you can make a cURL request with the following structure


```bash
java_home_knox=$(ps aux | grep "[j]ava.*gateway.jar" | awk '{print $11}' | xargs dirname | xargs dirname)
echo -n | openssl s_client -connect $HOSTNAME:8443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /tmp/knoxcert.crt
keytool -import -noprompt -trustcacerts -alias "knox-cert" -file "/tmp/knoxcert.crt" -keystore "$java_home_knox/jre/lib/security/cacerts" -storepass "changeit"

curl -i -k "https://$HOSTNAME:8443/gateway/default/service-test?username=admin&password=admin-password"
```


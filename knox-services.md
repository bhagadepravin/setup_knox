# Knox services

##### How to connect

- [WebHDFS](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#webhdfs)
- [HIVE](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Hive)
- [YARN](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#yarn)
- [Kafka](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#kafka)
- [Solr](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#solr)
- [Oozie](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#oozie)
- [HBase](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#hbase)
- [Spark](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#spark)
- [HDFS](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#hdfs)
- [Livy](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#livy)
- [ElasticSearch](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#elasticsearch)
- [Knox UI Service Details](https://github.com/bhagadepravin/setup_knox/blob/main/knox-services.md#knox-ui-service-details)



## WebHDFS
Make sure WebHDFS must be enabled in the hdfs-site.xml configuration file and exposes the API on each NameNode and DataNode.
```
<property>
    <name>dfs.webhdfs.enabled</name>
    <value>true</value>
</property>
```

`curl -ik -u Username:Password -X GET "https://{gateway-host}:{gateway-port}/gateway/default/webhdfs/v1/?op=LISTSTATUS"`

## [HIVE](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Hive)

Connect to hive using Knox.
```
jdbc:hive2://{gateway-host}:{gateway-port}/;ssl=true;sslTrustStore={gateway-trust-store-path};trustStorePassword={gateway-trust-store-password};transportMode=http;httpPath={gateway-path}/{cluster-name}/hive
```

[HiveServer2 HA](https://knox.apache.org/books/knox-2-0-0/user-guide.html#HiveServer2+HA)
```
<provider>
    <role>ha</role>
    <name>HaProvider</name>
    <enabled>true</enabled>
    <param>
        <name>HIVE</name>
        <value>maxFailoverAttempts=3;failoverSleep=1000;enabled=true;zookeeperEnsemble=machine1:2181,machine2:2181,machine3:2181;zookeeperNamespace=hiveserver2</value>
   </param>
</provider>

...
...
<service>
    <role>HIVE</role>
</service>
```

```
echo -n | openssl s_client -connect ${knoxserver}:8443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /tmp/knoxcert.crt
keytool -import -file /tmp/knoxcert.crt -keystore /tmp/knox.jks -alias knox-Cert -storepass changeit

beeline -u "jdbc:hive2://KnoxserverInternalHostName:8443/;ssl=true;sslTrustStore=/tmp/knox.jks;trustStorePassword=changeit;transportMode=http;httpPath=gateway/default/hive" -n <username> -p <password>
```

```
<service> 
<role>HIVE</role> 
<url>http://{{hive_server_host}}:{{hive_http_port}}/{{hive_http_path}}</url> 
<param><name>replayBufferSize</name><value>32</value></param> 
<param> 
<name>httpclient.connectionTimeout</name> 
<value>30m</value> 
</param> 
<param> 
<name>httpclient.socketTimeout</name> 
<value>30m</value> 
</param>
```

## YARN
[Yarn](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Yarn)
[Yarn Examples via cURL](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Yarn+Examples+via+cURL)

curl -ikv -u guest:guest-password -X GET 'https://localhost:8443/gateway/default/resourcemanager/v1/cluster'

## KAFKA
[Kafka](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Kafka)
```
<service>
    <role>KAFKA</role>
    <url>http://<kafka-rest-host>:<kafka-rest-port></url>
</service>
```

```
# 0. Getting topic info

curl -ikv -u guest:guest-password -X GET 'https://localhost:8443/gateway/default/kafka/topics'

# 1. Publish message to topic

curl -ikv -u guest:guest-password -X POST 'https://localhost:8443/gateway/default/kafka/topics/TOPIC1' -H 'Content-Type: application/vnd.kafka.json.v2+json' -H 'Accept: application/vnd.kafka.v2+json' --data '"records":[{"value":{"foo":"bar"}}]}'

````

## SOLR
[Solr](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Solr)

```
<service>
    <role>SOLR</role>
    <version>6.0.0</version>
    <url>http://<solr-host>:<solr-port></url>
</service>
<service>
    <role>SOLRAPI</role>
    <version>5.5.0</version>
    <url>http://<solr-host>:<solr-port></url>
</service>
```
Solr Examples via cURL
```
# 0. Query collection

curl -ikv -u guest:guest-password -X GET 'https://localhost:8443/gateway/default/solr/select?q=*:*&wt=json'

# 1. Query cluster status

curl -ikv -u guest:guest-password -X POST 'https://localhost:8443/gateway/default/solr/admin/collections?action=CLUSTERSTATUS' 
```

## OOZIE
https://knox.apache.org/books/knox-2-0-0/user-guide.html#Oozie+Example+via+cURL

## HBASE
[Hbase:](https://knox.apache.org/books/knox-2-0-0/user-guide.html#HBase)
[HBase REST API Setup](https://knox.apache.org/books/knox-2-0-0/user-guide.html#HBase+REST+API+Setup)
[HBase via cURL](https://knox.apache.org/books/knox-2-0-0/user-guide.html#HBase+via+cURL)
```
%  curl -ik -u guest:guest-password\
 -H "Accept:  application/json"\
 -X GET 'https://localhost:8443/gateway/sandbox/hbase/version'
```


## SPARK

For Spark UI + knox

```
To access the logs using Knox URL you need to add 'knox' user to 'spark.history.ui.admin.acls'

Please add below and restart the service let me know if that helps.

# spark.history.ui.admin.acls=knox
```


## HDFS

Access HDFS UI using Knox in HDP 3.1.4
```
HDP 3.1.4.0-.315
Ambari 2.7.4

The way HDFSUI is accessed in hdp 3.x is different than the HDP 2.6.5

There are two ways to access HDFSUI using Knox.

1. latest way is to use below url:

https://<knox-host>:8443/gateway/default/hdfs?host=http://<namenode-host>:50700

We were able to access the HDFSUI using Knox.

2. To access HDFSUI using below URL, we have to make changes

https://<knox-host>:8443/gateway/default/hdfs

Edit the topology file and add version to it.

<service>
<role>HDFSUI</role>
<version>2.7.0</version>
<url>http://c4112-node2.knox.com:50070/</url>
</service>


+++++++
Regarding the error with version 2.7.0, Please follow below steps:

1. Stop Knox server
2. Download hdfsui.tar.gz from the attachment, you need to replace xml files to below locations.

cd /usr/hdp/current/knox-server/data/services/hdfsui/2.7.0/
mv * /tmp

# untar hdfsui.tar.gz
place service.xml and rewrite.xml from  hdfsui.tar.gz to "/usr/hdp/current/knox-server/data/services/hdfsui/2.7.0/"

3. mv /usr/hdp/current/knox-server/data/deployments /usr/hdp/current/knox-server/data/deployments_bk1

4. Start Knox server

5. Access HDFSUI using Knox
https://knox-hostname:8443/gateway/default/hdfs

make sure topology has version 2.7.0

if it still does not work.

test the below curl cmd:


curl -i -k -u user-X GET https://knox-hostname:8443/gateway/default/hdfs/jmx?qry=java.lang:type=Memory
+++++
```

# [Livy](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Livy+Server)
```
<service>
  <role>LIVYSERVER</role>
  <url>http://<livy-server>:8998</url>
</service>
```

# [Elasticsearch](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Elasticsearch)

```
 <service>
   <role>ELASTICSEARCH</role>
   <url>http://<elasticsearch-rest-server>:9200/</url>
   <name>elasticsearch</name>
 </service>
```

- [Elasticsearch via Knox Gateway](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Elasticsearch+via+Knox+Gateway)
1. Elasticsearch Node Root Query
```
curl -i -k -u username:password -H "Accept: application/json"  -X GET  "https://{gateway-hostname}:{gateway-port}/gateway/{topology-name}/elasticsearch"

or

curl -i -k -u username:password -H "Accept: application/json"  -X GET  "https://{gateway-hostname}:{gateway-port}/gateway/{topology-name}/elasticsearch/"
```

## [Knox UI Service Details](https://knox.apache.org/books/knox-2-0-0/user-guide.html#UI+Service+Details)
- Name Node UI
    - https://{gateway-host}:{gateway-port}/gateway/default/hdfs
- Job History UI
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{cluster-name}/jobhistory
- Oozie UI
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{cluster-name}/oozie/
- HBase UI
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{cluster-name}/hbase/webui/
- Yarn UI
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{cluster-name}/yarn
- Spark UI
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{cluster-name}/sparkhistory
- Ambari UI
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{cluster-name}/ambari/
- Ranger Admin Console
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{cluster-name}/ranger/
- Atlas UI
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{topology}/atlas/index.html
- Zeppelin UI
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{topology}/zeppelin/
- Nifi UI
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{topology}/nifi-app/nifi/
- Hue UI
    - https://{gateway-host}:{gateway-port}/{gateway-path}/{topology}/hue/



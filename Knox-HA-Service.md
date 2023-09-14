# [Default Service HA support](https://knox.apache.org/books/knox-2-0-0/user-guide.html#Default+Service+HA+support)



```
<provider>
     <role>ha</role>
     <name>HaProvider</name>
     <enabled>true</enabled>
     <param>
         <name>OOZIE</name>
         <value>maxFailoverAttempts=3;failoverSleep=1000;enabled=true</value>
     </param>
     <param>
         <name>HBASE</name>
         <value>maxFailoverAttempts=3;failoverSleep=1000;enabled=true</value>
     </param>
    <param>
        <name>HIVE</name>
        <value>maxFailoverAttempts=3;failoverSleep=1000;enabled=true;zookeeperEnsemble=machine1:2181,machine2:2181,machine3:2181;zookeeperNamespace=hiveserver2</value>
   </param>
    <param>
        <name>KAFKA</name>
        <value>maxFailoverAttempts=3;failoverSleep=1000;enabled=true;zookeeperEnsemble=machine1:2181,machine2:2181,machine3:2181</value>
   </param> 
    <param>
        <name>SOLR</name>
        <value>maxFailoverAttempts=3;failoverSleep=1000;enabled=true;zookeeperEnsemble=machine1:2181,machine2:2181,machine3:2181</value>
   </param>    
</provider>
```

```
<service>
    <role>HIVE</role>
</service>
<service>
    <role>KAFKA</role>
</service>
<service>
    <role>SOLR</role>
    <version>6.0.0</version>
</service>
```

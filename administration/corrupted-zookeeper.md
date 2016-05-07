# Restoring a corrupted zookeeper


If the HBase server did not start correctly and the file
`/opt/atsd/hbase/logs/hbase-axibase-zookeeper-{hostname}.out` contains:

```java
 java.io.IOException: Failed to process transaction type: 1 error: Keeper ErrorCode = NoNode for /hbase                           
     at org.apache.zookeeper.server.persistence.FileTxnSnapLog.restore(FileTxnSnapLog.java:153)                                    
     at org.apache.zookeeper.server.ZKDatabase.loadDataBase(ZKDatabase.java:223)                                                  
     at org.apache.zookeeper.server.ZooKeeperServer.loadData(ZooKeeperServer.java:250)                                             
     at org.apache.zookeeper.server.ZooKeeperServer.startdata(ZooKeeperServer.java:377)                                            
     at org.apache.zookeeper.server.NIOServerCnxnFactory.startup(NIOServerCnxnFactory.java:122)                                    
     at org.apache.zookeeper.server.ZooKeeperServerMain.runFromConfig(ZooKeeperServerMain.java:112)                                
     at org.apache.hadoop.hbase.zookeeper.HQuorumPeer.runZKServer(HQuorumPeer.java:85)                                             
     at org.apache.hadoop.hbase.zookeeper.HQuorumPeer.main(HQuorumPeer.java:70)                                                    
 Caused by: org.apache.zookeeper.KeeperException$NoNodeException: KeeperErrorCode = NoNode for /hbase                              
     at org.apache.zookeeper.server.persistence.FileTxnSnapLog.processTransaction(FileTxnSnapLog.java:211)                         
     at org.apache.zookeeper.server.persistence.FileTxnSnapLog.restore(FileTxnSnapLog.java:151)
     ... 7 more                                                           
```
Then execute the following steps to fix the issue:

> Note: If your ATSD installation has replication setup according [replication
guide](replication.md "ATSD Replication"), execute the `add_peer` commands on the master machine again
to restart replication after you have restored the corrupted zookeeper using this guide.*

Stop HBase:

```sh
 /opt/atsd/hbase/bin/stop-hbase.sh                                        
```

Make sure that hbase has stopped:

```sh
 ps -ef | grep '[h]base'
```

If there is an output, kill processes:

```sh
 ps -ef | grep '[h]base' | awk '{print $2}' | xargs kill
```

Make sure that HBase has stopped:

```sh
 ps -ef | grep '[h]base'
```

If there is still an output, kill processes with flag –9:

```sh
 ps -ef | grep '[h]base' | awk '{print $2}' | xargs kill -9
```

Make sure that HBase has stopped:

```sh
 ps -ef | grep '[h]base'
```

Determine folder that contains zookeeper temporary files. This folder is
specified in `/opt/atsd/hbase/conf/hbase-site.xml` . Property has the
name `hbase.zookeeper.property.dataDir`. For example:

```sh
 /opt/atsd/hbase/zookeeper                                                
```

Remove this folder:

```sh
 rm -r  /opt/atsd/hbase/zookeeper                                         
```

Create a new folder with the same name:

```sh
 mkdir /opt/atsd/hbase/zookeeper                                          
```

Start HBase:

```sh
 /opt/atsd/hbase/bin/start-hbase.sh                                       
```

Check HBase shell:

```sh
 /opt/atsd/hbase/bin/hbase shell                                          
```

The console should start, execute the `list` command:

```sh
 list                                                                     
```

The output should be a list of tables, close the console:

```sh
 exit                                                                     
```

Start ATSD:

```sh
 /opt/atsd/atsd/bin/start-atsd.sh
```

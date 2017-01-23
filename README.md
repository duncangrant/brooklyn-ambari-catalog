Hadoop MapReduce cluster deployed using [ Brooklyn-Ambari ](https://github.com/brooklyncentral/brooklyn-ambari)
===

This project contains blueprints for [Apache Ambari](https://ambari.apache.org) servers and for deploying
[ hadoop MapReduce ] to those servers.


This blueprint currently uses the following dependencies:

- Apache Ambari **2.2.0.0**
- HDP stack definition **2.3**

## Running the blueprint

Add the catalog to your Brooklyn deployment using the cli:

    br add-catalog catalog.bom

You will now have a template installed that will be available from the quick launch
when you add an application through the Brooklyn UI.

Or you could deploy from a blueprint as shown below.

### Sample Blueprint

Below is a sample YAML blueprint that can be used to deploy Apache Ambari
to AWS:
 
    location:
      aws-ec2:us-east-1:
        minRam: 8192
        osFamily: ubuntu
        osVersionRegex: 12.*

    services:
      - type: ambari-map-reduce-application
        brooklyn.config:
          ambari.security.group: test-ambari

To use the blueprint unchanged first create an AWS security group called "test-ambari" with
an inbound TCP rule for port 8080 and allowing all traffic within the security group.

## Architecture

The blueprint will deploy an application with the following architecture:

### Ambari Server

A single Ambari Server using a self installed postgresql db.  For production use this db should
be regularly backed up.

### Name Node cluster

A single node cluster with the following components deployed:

- ZOOKEEPER_CLIENT
- NAMENODE
- HBASE_MASTER
- HDFS_CLIENT
- YARN_CLIENT
- MAPREDUCE2_CLIENT
- ZOOKEEPER_SERVER
- HBASE_CLIENT
- APP_TIMELINE_SERVER
- RESOURCEMANAGER

Can be resized to greater than one up after deployment.

### Secondary Name Node Cluster

A single node cluster with the following components deployed:

- SECONDARY_NAMENODE
- HBASE_REGIONSERVER
- ZOOKEEPER_SERVER
- NODEMANAGER
- DATANODE
- JOURNALNODE

Can be resized to greater than one up after deployment.

### Data Node Cluster

By defualt this cluster will be deployed with 3 nodes.  This can be configured with the
ambari.data.cluster.version param.

- HBASE_REGIONSERVER
- ZOOKEEPER_SERVER
- NODEMANAGER
- DATANODE
- JOURNALNODE

Nodes in this cluster will automatically be replaced if a node is detected to have failed.

If you want to deploy a more complex Ambari blueprint see [ Brooklyn-Ambari ](https://github.com/brooklyncentral/brooklyn-ambari)

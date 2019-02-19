## Overview

Configuration and setup for an Enterprise Mongo Server using SSL certificates

## Quickstart

Add/edit an entry for "mongoserver" in the `/etc/hosts` file 

```
127.0.0.1	localhost mongoserver
```

Ensure that there is a directory called `mongodb` in the current folder,
start the server with the following command

```
/opt/mongodb-linux-x86_64-enterprise-ubuntu1804-4.0.6/bin/mongod --config ./mongod-config.yaml
```

Connect to the server using the command line client

```
mongo --ssl --port 27717 --host mongoserver admin --verbose --sslCAFile enterprise_mongo_ca/pki/ca.crt
```


## Configuration

### Directory structure

There are several projects that have been put together to get this setup running, these should all be cloned under one common directory 

```
git clone git@gitlab.mgmt.exchange:fiona.bianchi/enterprise_mongo_ca.git
git clone git@gitlab.mgmt.exchange:fiona.bianchi/enterprise_mongo_server.git
git clone git@gitlab.mgmt.exchange:fiona.bianchi/enterprise_mongo_config.git
git clone git@gitlab.mgmt.exchange:fiona.bianchi/enterprise_mongo_client.git
```

### SSL



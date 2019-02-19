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


## Configuration

### SSL



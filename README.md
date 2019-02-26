
## Overview

Configuration and setup for an Enterprise Mongo Server using SSL certificates

## Quickstart

Add/edit an entry for "mongoserver" in the `/etc/hosts` file 

```
127.0.0.1	localhost mongoserver
```

Ensure that there is a directory called `mongodb` in the current folder,

```
mkdir mongodb
```

start the server with the following command

```
/opt/mongodb-linux-x86_64-enterprise-ubuntu1804-4.0.6/bin/mongod --config ./mongod-config.yaml
```

Connect to the server using the command line client (from the `enterprise_mongo_client` directory) 

```
mongo --ssl --port 27717 --host mongoserver admin --verbose --sslCAFile ../enterprise_mongo_ca/pki/ca.crt --sslPEMKeyFile mongoclient-key-crt.pem
```

You must create the admin account ASAP once connected to the Mongo server, see the instructions below.  Once
created, exit the connection and start again with the following command

```
mongo --ssl --port 27717 --host mongoserver admin --verbose --sslCAFile ../enterprise_mongo_ca/pki/ca.crt --sslPEMKeyFile mongoclient-key-crt.pem -u admin -p adminpassword
```

You can now proceed to create other schema elements.  



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

Use the unencrypted key for the server and the signed certificate and concatenate to produce a single
file, e.g.

```
cat ../enterprise_mongo_server/pki/private/mongoserver.key ../enterprise_mongo_ca/pki/issued/mongoserver.crt > mongoserver-key-crt.pem 
```

### Encryption

#### Using a file

You can create a key for encryption using the following command

```
openssl rand -base64 32 > mongod-keyfile
```

The configuration file then needs an entry such as this

```
security:
  authorization: enabled
  enableEncryption: true
  encryptionKeyFile: /home/fiona/wd/dwp/cis/mongo_enterprise_ssl/enterprise_mongo_config/mongod-keyfile
```

It seems that a full path to the key is needed.


### Admin User

Connect to the server *before* enabling authorisation and create an admin user

```
use admin
db.createUser(
  {
    user: "admin",
    pwd: "adminpassword",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ],
    passwordDigestor:"server"
  }
)
```

Test the connection with this user

```
mongo --ssl --port 27717 --host mongoserver admin --verbose --sslCAFile ../enterprise_mongo_ca/pki/ca.crt --sslPEMKeyFile mongoclient-key-crt.pem -u admin -p adminpassword
```

It should be possible to run a command, e.g. `show dbs` without errors once connected like this



### People DB User

While still connected to MongoDB as admin, create the people DB and relevant user

```
use persondb
db.createUser(
  {
    user: "persondb",
    pwd: "personpass",
    roles: [
       { role: "readWrite", db: "persondb" }
    ],
    passwordDigestor:"server"
  }
)
```


### Test Data

Run the following to add some data

```
db.person.insert({
   id: "00001",
   firstname: "Angua",
   lastname: "Von Uberwald",
     address: {
       city: "Ankh Morpork",
       state: "The Shades"
     }
})

db.person.insert({
   id: "00002",
   firstname: "Agnes",
   lastname: "Nitt",
     address: {
       city: "Mad Stoat",
       state: "Lancre"
     }
})

db.person.insert({
   id: "00003",
   firstname: "Polly",
   lastname: "Perks",
     address: {
       city: "Munz",
       state: "Borogravia"
     }
})

db.person.insert({
   id: "00004",
   firstname: "Adorabelle",
   lastname: "Dearheart",
     address: {
       city: "Ankh Morpork",
       state: "The Bank of Ankh Morpork"
     }
})
```



---
layout: post
title: "How to install & configure MongoDB on Centos 7"
date: 2018-05-10 11:40:38 +0700
categories: database
tags: [mongodb, nosql]
---
### How to install & configure Mongodb on Centos 7

MongoDB is a free and open-source cross-platform document-oriented database program. Classified as a NoSQL database program, MongoDB uses JSON-like documents with schemas. MongoDB is developed by MongoDB Inc., and is published under a combination of the GNU Affero General Public License and the Apache License

This tutorial will guides you through installing MongoDB Community Edition on a CentOS 7 server. 

- [Prerequisites](#prerequisites)
- [Adding the MongoDB Repository](#adding-the-mongodb-repository)
- [Installing MongoDB](#installing-mongodb)
- [Importing an Example Dataset](#importing-an-example-dataset)
- [Enabling Authentication](#enabling-authentication)
- [Enabling Remote Access](#enabling-remote-access)

### Prerequisites

Before following this tutorial, make sure you have a regular, non-root user with `sudo` privileges. You can learn more about how to set up a user with these privileges from [here](https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-centos-quickstart). 

<!--break-->

### Adding the MongoDB Repository

The `mongodb-org` package does not exist within the default repositories for CentOS. However, MongoDB maintains a dedicated repository. Let's add it to our server.

```
$ sudo vi /etc/yum.repos.d/mongodb-org.repo
```

Then, visit the [Install on Red Hat](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#configure-the-package-management-system-yum) section of MongoDB’s documentation and add the repository information to the file above.

```
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Before we move to the next step, we should verify that the MongoDB repository exists within the `yum` utility. The `repolist` command will show you a list of configured repositories. 

```
$ yum repolist
repo id                          repo name
base/7/x86_64                    CentOS-7 - Base
epel/x86_64                      Extra Packages for Enterprise Linux 7 - x86_64 
extras/7/x86_64                  CentOS-7 - Extras
ius/x86_64                       IUS Community Packages for Enterprise Linux 7 - x86_64
mariadb                          MariaDB
mongodb-org-3.6/7                MongoDB Repository
nginx/x86_64                     nginx repo
updates/7/x86_64                 CentOS-7 - Updates
. . .
```

### Installing MongoDB

Once you have added MongoDB repository, run the following command to install MongoDB. 

```
$ sudo yum install mongodb-org
```

There are two `Is this ok [y/N]:` prompts. The first one permits the installation of the MongoDB packages and the second one imports a GPG key. The publisher of MongoDB signs their software and `yum` uses a key to confirm the integrity of the downloaded packages. At each prompt, type `Y` and then press the `ENTER` key.

Next, start the MongoDB service with the `systemctl` utility:

```
$ sudo systemctl start mongod
```

The `systemctl` utility did not provide a result after executing the `start` command, but we can check that the service started by viewing the end of the `mongod.log` file with the `tail` command:

```
$ sudo tail /var/log/mongodb/mongod.log
[initandlisten] WiredTiger message [1525952439:540366][19874:0x7f9c5b141b00], txn-recover: Set global recovery timestamp: 0
[initandlisten]
[initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/enabled is 'always'.
[initandlisten] **        We suggest setting it to 'never'
[initandlisten]
[initandlisten] ** WARNING: /sys/kernel/mm/transparent_hugepage/defrag is 'always'.
[initandlisten] **        We suggest setting it to 'never'
[initandlisten]
[initandlisten] Initializing full-time diagnostic data capture with directory '/var/lib/mongo/diagnostic.data'
[initandlisten] waiting for connections on port 27017
```

An output of **waiting for a connection** confirms that MongoDB has started successfully and we can access the database server with the MongoDB Shell. We also can use `netstat` command to check whether the MongoDB is listening on port “27017”.

```
$ netstat -antup | grep -i 27017
tcp        0      0 0.0.0.0:27017           0.0.0.0:*               LISTEN      -
```

To check the status of MongoDB service we can run the following command
```
$ sudo systemctl status mongod
● mongod.service - High-performance, schema-free document-oriented database
   Loaded: loaded (/etc/systemd/system/mongod.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2018-05-10 11:40:38 UTC; 2h 23min ago
     Docs: https://docs.mongodb.org/manual
  Process: 19865 ExecStartPre=/usr/bin/chmod 0755 /var/run/mongodb (code=exited, status=0/SUCCESS)
  Process: 19862 ExecStartPre=/usr/bin/chown mongod:mongod /var/run/mongodb (code=exited, status=0/SUCCESS)
  Process: 19861 ExecStartPre=/usr/bin/mkdir -p /var/run/mongodb (code=exited, status=0/SUCCESS)
 Main PID: 19874 (mongod)
   CGroup: /system.slice/mongod.service
           └─19874 /usr/bin/mongod --auth --quiet -f /etc/mongod.conf run
```
### Importing an Example Dataset

Unlike other database servers, MongoDB does not come with data in its `test` database. Since we don’t want to experiment with new software using production data, we will download a sample dataset from [Documentation Assets](https://github.com/mongodb/docs-assets/tree/primer-dataset). The JSON document contains a collection of inventories, which we’ll use to practice interacting with MongoDB and avoid causing harm to sensitive data.

```
$ cd /tmp
$ curl -LO https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/inventory.json
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 1118k  100 1118k    0     0  2113k      0 --:--:-- --:--:-- --:--:-- 2118k
$ mongoimport --db test --collection inventory --file /tmp/inventory.json
2018-05-10T18:53:02.274+0000    connected to: localhost
2018-05-10T18:53:02.470+0000    imported 3244 documents
```

### Enabling Authentication

Enabling access control on a MongoDB deployment enforces authentication, requiring users to identify themselves. When accessing a MongoDB deployment that has access control enabled, users can only perform actions as determined by their roles. Mongodb comes with several [built in roles](https://docs.mongodb.com/manual/reference/built-in-roles/) that provide the different levels of access commonly needed in database system. Below is the steps how to enable authentication on MongoDB.

```
$ mongo
MongoDB shell version v3.6.4
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.6.4
> use admin
switched to db admin
> db.createUser({ user: "root", pwd: "password", roles: [{role: "root", db: "admin"}] })
Successfully added user: {
        "user" : "root",
        "roles" : [
                {
                        "role" : "root",
                        "db" : "admin"
                }
        ]
}
```
Open `/etc/mongod.conf` and set option `authorization` to enabled 

```
security:
  authorization: enabled
```
Restart MongoDB to take effect.

```
$ sudo systemctl restart mongod 
```
Lets try user with authentication

```
$ mongo
MongoDB shell version v3.6.4
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.6.4
> use test
switched to db test
> show collections
2018-05-10T20:08:41.352+0000 E QUERY    [thread1] Error: listCollections failed: {
        "ok" : 0,
        "errmsg" : "not authorized on test to execute command { listCollections: 1.0, filter: {}, $db: \"test\" }",
        "code" : 13,
        "codeName" : "Unauthorized"
} :
_getErrorWithCode@src/mongo/shell/utils.js:25:13
DB.prototype._getCollectionInfosCommand@src/mongo/shell/db.js:941:1
DB.prototype.getCollectionInfos@src/mongo/shell/db.js:953:19
DB.prototype.getCollectionNames@src/mongo/shell/db.js:964:16
shellHelper.show@src/mongo/shell/utils.js:813:9
shellHelper@src/mongo/shell/utils.js:710:15
@(shellhelp2):1:1
> use admin
switched to db admin
> db.auth("root", "password")
1
> use test
switched to db test
> show collections
inventory
> db.inventory.find({}, {item: 1, qty: 1}).limit(5)
{ "_id" : ObjectId("5ab2f6709b8f98766c1083ce"), "item" : "Accuprint", "qty" : 3306 }
{ "_id" : ObjectId("5ada4760c9bdb06218ec39b2"), "item" : "33 Record and Vinyl", "qty" : 70 }
{ "_id" : ObjectId("5ada4760c9bdb06218ec39b3"), "item" : "33Ease", "qty" : 989 }
{ "_id" : ObjectId("5ada4760c9bdb06218ec39b4"), "item" : "3Deem Yourself", "qty" : 889 }
{ "_id" : ObjectId("5ada4760c9bdb06218ec39b5"), "item" : "3Demon", "qty" : 2209 }                                
```

Create additional users as needed

```
> db.createUser({user: "usertest", "pwd": "passwordtest", roles: [{role: "read", db:"test"}]})
Successfully added user: {
        "user" : "usertest",
        "roles" : [
                {
                        "role" : "read",
                        "db" : "test"
                }
        ]
}
> use admin
switched to db admin
> db.logout()
{ "ok" : 1 }
> use test
switched to db test
> db.auth("usertest", "passwordtest")
1
> show collections
inventory
> db.inventory.insert({"item": "Test", "qty": 17})
WriteResult({
        "writeError" : {
                "code" : 13,
                "errmsg" : "not authorized on test to execute command { insert: \"inventory\", ordered: true, $db: \"test\" }"
        }
})
```

Because user that we add only have `read` role access, the user can't add new data to the collections. To check current user that logged in to mongo shell, we can use command below

```
> db.runCommand({connectionStatus : 1})
{
        "authInfo" : {
                "authenticatedUsers" : [
                        {
                                "user" : "usertest",
                                "db" : "test"
                        }
                ],
                "authenticatedUserRoles" : [
                        {
                                "role" : "read",
                                "db" : "test"
                        }
                ]
        },
        "ok" : 1
}
```

### Enabling Remote Access

By default MongoDB bind to local interface only, which means no remote access to the MongoDB server. To allow remote access we need to change `bindIp` value in `/etc/mongod.conf`

If you set value of the `bindIp` option to 0.0.0.0 or remove the `bindIp` option, MongoDB will accept connection on any interfaces. 

```
# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0
```

Or bind to specific IP addresses

```
# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1,192.168.1.8
```

Restart MongoDB to take effect.

```
$ sudo systemctl restart mongod 
```



### Reference

1. [https://en.wikipedia.org/wiki/MongoDB](https://en.wikipedia.org/wiki/MongoDB){:target="_blank"}
2. [https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-centos-7](https://en.wikipedia.org/wiki/MongoDB){:target="_blank"}


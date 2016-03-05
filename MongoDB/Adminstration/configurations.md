#### [back](../admin_main.md)



MongoDB uses a configuration file for each mongod or mongos instance that contains all the settings and command options that will be used by this instance. The file has a [YAML format](http://www.yaml.org/start.html). As an example, below are some basic configurations:

````
processManagement:
   fork: true
net:
   bindIp: 127.0.0.1
   port: 27017
storage:
   dbPath: /srv/mongodb
systemLog:
   destination: file
   path: "/var/log/mongodb/mongod.log"
   logAppend: true
storage:
   journal:
      enabled: true
  
 ````


When you start your mongod or mongos instance, you need to specify the configuration file that will be used as shown below:

````
mongod --config /etc/mongod.conf

mongos --config /etc/mongos.conf
````

If you want to change a configuration option in the configuration file of a mongod or mongos instances, you will need to restart the instance to pick up the new changes.

For a complete list of all the configuration options available for MongoDB, please review [MongoDB documentation.](https://docs.mongodb.org/manual/reference/configuration-options/)


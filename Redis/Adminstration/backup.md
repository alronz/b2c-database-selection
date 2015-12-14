#### [back](admin_main.md)

Doing backup in Redis is so simple since it is basically taking snapshot of your dataset which already supported by Redis. Your data will be stored in an RDB file called dump.rdb file and will be stored in your redis directory. As has been explained before, you can configure Redis to take a snapshot of your data periodically as configured in your configuration file. Also you can get a snapshot manually by running the command SAVE or BGSAVE to be run as background process. To restore your data, you just need to move this dump file to your Redis directory and then start up your server and that will be all. If you don't know your Redis directory, just run the below command:

````
127.0.0.1:6379> CONFIG get dir

1) "dir"
2) "/user/UserName/redis-version/src"
````

To have a better backup to be used in disaster recovery, you can use snapshotting with replication so that you will have the same copy of your data in all your instances including masters and slaves. This will ensure that your data is save in case the master server crashed completely. 



#### [back](admin_main.md)

Neo4j supports automatic upgrade for upgrades between patch releases and within the same minor version. However, upgrading a major release is manual. In this section, I will show how to do manual and automatic upgrade.


##### Automatic Upgrade

As mentioned pre


To perform an automatic store upgrade:

Cleanly shut down the older version of Neo4j, if it is running.
Install Neo4j 2.3.2, and set it up to use the same database store directory (typically data/graph.db).
Make a copy of the database.
[Important]	
Important
It is strongly advised to make a copy of the database store directory at this time, to use as a backup in case rollback/downgrade is required. This is not necessary if a backup has been made using the online backup tool, available with Neo4j Enterprise.
Start up Neo4j.
Any database store upgrade required will occur during startup.




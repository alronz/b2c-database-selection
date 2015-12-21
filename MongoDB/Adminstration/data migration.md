MongoDB supports the two useful easy to use tools mongoexport and mongoimport that can be used to migrate data between differnt mongodb instances. By using mongoexport, you can export a certain collection data in a CSV or BSON format. It is also possible to provide a query so that you can export only a part of the data in a certain collection. Examples are given below:

Export data in csv format:

````
mongoexport --db users --collection contacts --type=csv --fields name,address --out /opt/backups/contacts.csv
````

As seen above you can specify which fields to export as well as the export path.

Export data in BSON format:

````
mongoexport --db sales --collection contacts --out contacts.json
````

Then to import the data, you can run the import command as shown below:

````
mongoimport --db users --collection contacts --file contacts.json
````



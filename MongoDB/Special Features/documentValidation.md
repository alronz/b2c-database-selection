Starting from MongoDB version 3.2, MongoDB supports a new feature called document validation. This is a useful feature that can be used to enforce some validation rules on the documents structure inside a particular collection. These rules will be checked when an insert or update is performed against the collection. If the validation rules have been violated, then an error or warning will be raised depending on the validationAction option. You can set the validation rules either when you create the collection using the db.createCollection() method with the validator option or with the collMod command with the validator option if the collection is already created. An example how to create a collection with some validation rules is shown below:

````
db.createCollection( "customers",
   { validator: { $or:
      [
         { phone: { $type: "double" } },
         { email: { $regex: /@yahoo\.com$/ } }
      ]
   }
} )
````

MongoDB validates the documents during updates or inserts. If you add a validation rules to an already existing collection, the existing documents will be validated only when modified. You can set the validationLevel option to either strict or moderate. If you chose strict, then all documents will be validated during inserts or updates. However, if you use the moderate option, then the documents will be validated during updates and inserts only if the documents fulfil the validation criteria.  If you want to disable validation, you can set validationLevel to off.



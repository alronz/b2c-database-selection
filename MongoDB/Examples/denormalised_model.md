

Complete denormalized model is in the order document:

```json
 {
	"_id": "ObjectID",
	"orderstatus": "String",
	"totalprice": "Double",
	"orderdate": "Date",
	"orderpriority": "String",
	"clerk": "String",
	"shippriority": "String",
	"comment": "String",
	"customer": "Object<Customer>",
	"LineItems": "Array<lineitem>"
}
```

where customer is:

```json
{
	"_id": "ObjectID",
	"name": "String",
	"address": "String",
	"phone": "Double",
	"acctbal": "Double",
	"mktsegment": "String",
	"comment": "String",
	"nation": "Object<Nation>"
}
```

And lineitem is :

```json
{
	"_id": "ObjectID",
	"quantity": "Double",
	"extende dprice": "Double",
	"discount": "Double",
	"tax": "Double",
	"returnflag": "Boolean",
	"linestatus": "String",
	"shipdate": "Date",
	"commitdate": "Date",
	"receiptdate": "Date",
	"shipinstruct": "String",
	"shipmode": "String",
	"comment": "String",
	"partsupp": "Object<partsupp>"

}
```

partsupp object :

```json
{
	"_id": "ObjectID",
	"availqty": "Double",
	"supplycost": "Double",
	"comment": "String",
	"part": "Object<part>",
	"supplier": "Object<supplier>"
}
```

part object:

```json
{
	"_id": "String",
	"name": "String",
	"mfgr": "String",
	"brand": "String",
	"type": "String",
	"size": "Double",
	"container": "String",
	"retailprice": "Double",
	"comment": "String"
}
```

supplier object:

```json
{
	"_id": "ObjectID",
	"name": "String",
	"address": "String",
	"phone": "Double",
	"acctbal": "Double",
	"comment": "String",
	"nation": "Object<nation>"
}
```

nation object is :

```json
{
	"_id": "ObjectID",
	"name": "String",
	"comment": "String",
	"region": "Object<region>"
}
```

region object:

```json
{
	"_id": "ObjectID",
	"name": "String",
	"comment": "String"
}
```


Detailed example:

order object:


```json
{
 	"_id": "7821ef4d-8e8c-46e0-950d-83c245b9bee8",
 	"orderstatus": "Open",
 	"totalprice": "233",
 	"orderdate": "2015-12-21 10:51:25",
 	"orderpriority": "High",
 	"clerk": "John",
 	"shippriority": "High",
 	"comment": "This is an order",
 	"customer": {
 		"_id": "f4005128-a7d0-11e5-bf7f-feff819cdc9f",
 		"name": "Bilal",
 		"address": "Street 1",
 		"phone": "1223456",
 		"acctbal": "212",
 		"mktsegment": "some text",
 		"comment": "some text",
 		"nation": {
 			"_id": "0aa770e6-a7d1-11e5-bf7f-feff819cdc9f",
 			"name": "US",
 			"comment": "some text",
 			"region": {
 				"_id": "18f228bc-a7d1-11e5-bf7f-feff819cdc9f",
 				"name": "Texas",
 				"comment": "some text"
 			}
 		}
 	},
 	"LineItems": [{
 		"_id": "2ddbd282-a7d1-11e5-bf7f-feff819cdc9f",
 		"quantity": "1",
 		"extende dprice": "200",
 		"discount": "20",
 		"tax": "2",
 		"returnflag": "false",
 		"linestatus": "available",
 		"shipdate": "2015-12-21 10:51:25",
 		"commitdate": "2015-12-21 10:51:25",
 		"receiptdate": "2015-12-21 10:51:25",
 		"shipinstruct": "some text",
 		"shipmode": "DHL",
 		"comment": "some text",
 		"partsupp": {
 			"_id": "62353e7e-a7d1-11e5-bf7f-feff819cdc9f",
 			"availqty": "20",
 			"supplycost": "220",
 			"comment": "some text",
 			"part": {
 				"_id": "75f28eda-a7d1-11e5-bf7f-feff819cdc9f",
 				"name": "Tshirt",
 				"mfgr": "Boss",
 				"brand": "Boss",
 				"type": "sport",
 				"size": "40",
 				"container": "some text",
 				"retailprice": "230",
 				"comment": "some text"
 			},
 			"supplier": {
 				"_id": "968a0d3a-a7d1-11e5-bf7f-feff819cdc9f",
 				"name": "Boss Supplier",
 				"address": "street 2",
 				"phone": "212323",
 				"acctbal": "2933",
 				"comment": "some text",
 				"nation": {
 					"_id": "0aa770e6-a7d1-11e5-bf7f-feff819cdc9f",
 					"name": "US",
 					"comment": "some text",
 					"region": {
 						"_id": "18f228bc-a7d1-11e5-bf7f-feff819cdc9f",
 						"name": "Texas",
 						"comment": "some text"
 					}
 				}
 			}
 		}
 	}]
 }
 ```

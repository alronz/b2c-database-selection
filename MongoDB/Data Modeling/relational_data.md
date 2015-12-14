#### [back](data_modeling_main.md)

In this section, I will show how you can use MongoDB to model common relations between you data such as one-to-one, one-to-many, and many-to-many relations. For each relation, we can either use the normalised model or the denormalised model. I have explained in the [previous section](data_layout.md) the difference between the two models and the benefits and shortcomings for each.


##### One-To-One Relationship


Usually, it is a good idea to model such relation using embedded documents. An example is if you want to model the relationship between product and product details models. Each product has one product details. Assuming we are modelling them using references, they will look like this:

Product:

````
{
	sku: “SomeSkuID”
	name: “Dell XPS”
}
````

ProductDetails:

````
{
	productSku: “SomeSkuID”,
	size: “20”,
	colour: "Red"
}
````

However if you model it using the embedded document way, you can do that as shown below:


Product:

````
{
	sku: “SomeSkuID”
	name: “Dell XPS”
	productDetails:
	{
	productSku: “SomeSkuID”,
	size: “20”,
	colour: "Red"
	}
}
````

##### One-To-Many Relationship

An example of a one to many relationship, is the relationship between customer and his orders. Each customer can have many addresses but each address is mapped only for one customer. To map this relationship using references, we can do that as shown below:

Customer:

````
{
    id: "SomeCustomerID"
	firstName: “John”,
	lastName: “Robert”,
	phoneNumber: “121212”,
	email: "email@yahoo.com"
}
````

Address:

````
{
    id: "SomeAddressID"
	street: “street 1”,
	city: “Munich”,
	country: "Germany",
	postCode: "212",
	customerID: "SomeCustomerID"
}

{
    id: "SomeOtherAddressID"
	street: “street 2”,
	city: “Munich”,
	country: "Germany",
	postCode: "3333",
	customerID: "SomeCustomerID"
}
````


If we want to model this relation using the denormalised model. We will do that by embedding the addresses inside the customer documents as shown below:

````
{
    id: "SomeCustomerID"
	firstName: “John”,
	lastName: “Robert”,
	phoneNumber: “121212”,
	email: "email@yahoo.com"
	addresses: [
	{
	id: "SomeAddressID"
	street: “street 1”,
	city: “Munich”,
	country: "Germany",
	postCode: "212"
	},
	
	{
	id: "SomeOtherAddressID"
	street: “street 2”,
	city: “Munich”,
	country: "Germany",
	postCode: "3333"
	}	]
}
````



##### Many-To-Many Relationship

In general, it would be a better idea to use the normalised way using references in order to model many-to-many relationship to prevent data redundancy. Below I will show how it will be if we model this relationship using both normalised and denormalised models.

An example if you want to model the many-to-many relationship between products and categories. Each product can be in more than one category and each category can have more than one product. If we use the normalised model to define this relationship using references, we can do something like this:

Categories:

````
{
	id: “1”,
	name: “Dell Laptops”,
	parent: “Laptops”,
}


{
	id: “2”,
	name: “Computers & Laptops”,
	parent: “Electronics”,
}
````

Product:

````
{
	sku: “SomeSkuID”,
	name: “Dell XPS”,
	size: “20”,
	colour: "Red",
	cat: [ "1" , "2"]
}
````

However if you want to embed the categories data inside each products as seen below, the category information will be unnecessary duplicated in each product. 


````
{
	sku: “SomeSkuID”,
	name: “Dell XPS”,
	size: “20”,
	colour: "Red",
	cat: [
	{
	id: “1”,
	name: “Dell Laptops”,
	parent: “Computers & Laptops”,
	 } , 
	 {
	id: “2”,
	name: “Computers & Laptops”,
	parent: “Electronics”,
	 } ]
}


{
	sku: “SomeOtherSkuID”,
	name: “IBM lenovo”,
	size: “20”,
	colour: "Red",
	cat: [
	{
	id: “3”,
	name: “IBM Laptops”,
	parent: “Computers & Laptops”,
	 } , 
	 {
	id: “2”,
	name: “Computers & Laptops”,
	parent: “Electronics”,
	 } ]
}
````

As seen above the the category with id 2 was repeated twice in both products. 



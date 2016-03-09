
#### [back](search_data_main.md)

Redis supports a sort command that can be used to sort most of Redis data structures such as lists, sets and sorted sets. This sort command can sort your data ascendingly, descendingly or alphabetically. You can also sort by a specific pattern or limit the returned data. Another useful feature of the sort command, is that you can also sort by external keys. In the below section, I will show how you can use the Sort command.


##### Sort ascendancy, despondingly or alphabetically

Using the Sort command, you can sort your list, set or sorted sets easily either ascendancy, despondingly or alphabetically. An example for each is shown below:

````
SORT mylist DESC

SORT mylist ASC

SORT mylist ALPHA
````


##### Limit the returned sorted data

The number of returned elements can be limited using the Limit modifier as shown below:

````
SORT mylist LIMIT 0 5 ALPHA DESC
````

##### Sort by a certain pattern

You can also sort only the elements that are matching a certain pattern using GET Pattern. Example is shown below:


````
SORT mylist GET object_* 
````


##### Sort by an external key

Another useful feature of the sort command, is that you can sort the elements in a list, set or a sorted set using external key. For example if you have a list that you want to sort based on a certain weight that exists in another data structure, then you can sort the list using the weight as shown below:

````
SORT mylist BY weight_*
````
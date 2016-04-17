

### [Neo4j](../Neo4j.md) > [Query Model](Query Model.md) > Regular Expressions Support
___


Neo4j supports filtering results using regular expressions. The java regular expressions syntax is used which includes some flags to specify how strings are matched such as "?m" for multiline, "?i" for case-insensitive, and "?s" for dotall. The regular expressions can be used in the MATCH clause with WHERE statement.  Some examples are shown below:


````
MATCH (p:Product)
WHERE p.name =~ 'Lap.*'
RETURN n
````

In the above example, we are searching for all products that starts with "Lap" string. Results will contain products with names like "laptop" and etc.


Similar to java regular expressions, you can use a forward slash to escape some characters as seen in the example below:

````
MATCH (p:Product)
WHERE p.name =~ 'T\\-Shirt'
RETURN n
````

The above example will return a product with name having the "-" character like "T-Shirt".





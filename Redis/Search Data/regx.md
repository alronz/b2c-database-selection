#### [back](search_data_main.md)

Redis supports some search commands that uses regular expressions such as KEYS which searches all keys in a Redis instance. Additionally Redis supports the SCAN command which is used to search and iterate through all keys and values (SSCAN, HSCAN, and ZSCAN are the same but specific for each data type).

KEYS and SCAN can use global-style regular expression patterns. Example of what can be used is shown below:

* h?llo matches hello, hallo and hxllo
* h*llo matches hllo and heeeello
* h[ae]llo matches hello and hallo, but not hillo
* h[^e]llo matches hallo, hbllo, ... but not hello
* h[a-b]llo matches hallo and hbllo


[Home](../../index.md)

[MongoDB Content](../MongoDB.md)
___

# MongoDB > Special Features > GridFS


MongoDB supports a powerful feature used to store and manage large files called GridFS. This feature can be used in many use cases such as content management system where you need to store and manage big binary data with metadata information. Other use case is in health-care related application where you want to show all patient information in one place. Part of the patient information can be a large binary data such as the x-ray, and MRI images which can be difficult to store in a normal documents in MongoDB because of the 16 MB BSON size limit. Beside storing large files, GridFS can be used when your filesystem limits the number of files in a single directory or when you want later to retrieve only part of your files instead of loading the whole file into memory.

GridFS simply store large binary files by splitting them into smaller parts called "chunks" and then store them into MongoDB. Then when you want to retrieve the file, GridFS will combine all the chunks and return the file for you. 

When you store the binary files by using GridFS, it will break the files into chunks and store it in the fs.chunks collection. The chunk size is 255K. GridFS uses another collection called fs.files to store the metadata information of the file. 


You can use GridFS to store and retrieved files by either using the "mongofiles" shell command or by any of the client drivers that support GridFS.

You can also perform range queries on the file chunks to retrieve only a certain part of the file and GridFS uses unique and compound index on the "files_id" and "n" fields of the chunks collection to allow for efficient retrieval of files.


For more information about GridFS, please have a look at [MongoDB documentation.](https://docs.mongodb.org/manual/core/gridfs/) 
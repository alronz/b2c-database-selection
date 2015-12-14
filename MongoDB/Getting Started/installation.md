#### [back](getting_started_main.md)

MongoDB can be installed on most platforms. I will explain below how to install it on Windows, Linux Red Hat & CentOS, and Mac OS X. For the full detailed installation instructions for all other platform, please have a look to [mongoDB documentations](https://docs.mongodb.org/manual/).  

##### Windows Systems

MongoDB is supported on most of Windows versions except for Windows XP. To install MongoDB, first you need to download the latest production version of MongoDB that is suitable for your Windows architecture (32 or 64 bit) from [MondoDB website](https://www.mongodb.org/downloads?_ga=1.147842807.1782745976.1443197794#production). 
After downloading the MongoDB installation file (a file with .msi extension), double click the file to start the installation. Follow the guided installation screens till the end which should be easy to follow. 
When the installation is completed, you can start MongoDB using the following commands assuming you have installed MongoDB in your C driver:

First create a data directory to store your data:

````
md \data\db
````

Then start MongoDB server:

````
C:\mongodb\bin\mongod.exe
````

To connect to MongoDB command run the below:

````
C:\mongodb\bin\mongo.exe
````

##### Linux Red Hat & CentOS 

MongoDB can be installed on Linux Red Hat Enterprise or CentOS versions 5, 6, and 7 using the package management system (yum). First we need to configure the yum by creating the below file:

````
vi /etc/yum.repos.d/mongodb-org-3.2.repo
````

Then paste the below inside the file and save it:

````
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=0
enabled=1
````

Finally install MongoDB using below command:

````
sudo yum install -y mongodb-org
````


After installation is complete, you can run MongoDB easily using below command:

````
sudo service mongod start
````

However you should make sure that you have configured SELinux before running MongoDB. You just need to change the SELINUX settings by editing the file /etc/selinux/config with below two settings:

````
SELINUX=disabled
SELINUX=permissive
````

And don't forget to give permissions for the folders that will be used by MongoDB "/var/lib/mongo and /var/log/mongodb".

##### Mac OS X

You can install MongoDB on Mac OS X using either the Homebrew package manager or by building it directly from source. To Install it using Homebrew package manager, please use the below simple commands:


````
brew update
````

````
brew install mongodb
````

To install MongoDB from source, you first need to download the binary files from [MongoDB download page](https://www.mongodb.org/downloads). Then extract the downloaded files using below command:

````
tar -zxvf mongodb-downloaded-files.tgz
````

Finally export the path to the environment configuration using:

````
export PATH=<mongodb-install-directory>/bin:$PATH
````


To run MongoDB, first create a directory to store MongoDB's data and give it the right permissions:

````
mkdir -p /data/db
````

Then run MongoDB using:

````
mongod
````





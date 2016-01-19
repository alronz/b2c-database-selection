#### [back](getting_started_main.md)



In this section, I will explain how to install Neo4j in the famous operating systems such as windows, Linux and Mac OSX.

##### Windows OS

You can install Neo4j easily by using windows installer following the below three simple steps:

1- Go to Neo4j [download page](http://neo4j.com/download/). 

2- Choose and download the version that goes with your windows architecture.

3- Open the installer and follow the promote steps.

A more advanced method to have more control over the installation can be done using the Windows PowerShell Module supported by Neo4j. For more details about how to use the PowerShell, please have a look to the [documentation](http://neo4j.com/docs/stable/powershell.html).



##### Linux


To install Neo4j in Linux systems, you can easily use the apt-get package management command line as shown blow:

````
sudo apt-get install neo4j=2.3.1
````

Another way to install Neo4j in Linux is by downloading the distribution package by following the steps below:

1- Go to Neo4j [download page](http://neo4j.com/download/). 

2- Choose and download the appropriate version for your platform.

3- Extract the downloaded tar file using the below command:


````
tar -xf <filename>
````

4- Start the server using the command:

````
Run: ./bin/neo4j console
````


#### Mac OSX

Neo4j provides a .dmg installer, so you can install Neo4j easily in Mac OSX by following the following three simple steps :


1- Go to Neo4j [download page](http://neo4j.com/download/). 

2- Download the .dmg file.

3- Open the installer then drag the Neo4j icon to the application folder.



The server can be started and stopped from the terminal using "neo4j start" or "neo4j stop". You can also use "neo4j-shell" to interact with Neo4j using command-line.
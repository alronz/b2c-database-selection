

### [Redis ](../Redis.md) > [Basics](Basics.md) > Installation
___


Redis is very easy to install and it will take only couple of minutes for you to install it on your machine. This is because it comes with a simple shell that provides you with everything you need and it doesn't have any external dependencies that you need to consider before installation.
 
There are several options available for you to install Redis depending on your system configurations. I will show here how to install it using the most two commonly used options.

Since Redis doesn't officially support windows, I will only show the installation steps on linux-based and Mac systems. However to install Redis in windows, there is a [windows port option](https://github.com/MSOpenTech/redis) developed by Microsoft Open Technologies that can be used.


#### 1- Building from the source:

Using the below scrip you can simply download, extract and build redis:

```
$ wget http://download.redis.io/releases/redis-3.0.5.tar.gz
$ tar xzf redis-3.0.5.tar.gz
$ cd redis-3.0.5
$ make

```

Now the compiled binaries can be found inside src folder. To run the server run the below:

```
$ cd src
$ ./redis-server

```

You will get a warning stating that the configuration file (redis.conf) couldn't be found which is ok for now since Redis will use the defaults. We will talk about Redis configuration on details on later [section](../Administration and Maintenance/Configuration.md).

#### 2- Using package managers:

Installing redis using package managers is even more convienient and preferable for many users. Below you can see how to install Redis using the popular package managers:

Debian/Ubuntu:

```sudo apt-get install redis-server
```Fedora/Redhat/CentOS:

```
sudo yum install redis

```Mac OS X:

```
port install redis

```

```
brew install redis```
Then after installation is complete, you can run Redis using:

```redis-server /usr/local/etc/redis.conf```
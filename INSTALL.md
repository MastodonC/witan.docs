# Installing Witan
Witan is almost entirely written in [Clojure](http://clojure.org/) and [ClojureScript](http://clojure.org/clojurescript). It uses a '[microservices](https://en.wikipedia.org/wiki/Microservices)' architecture which means there are several applications in orbit that contribute to the overall operation:

| Service        | Location                                     | Description                                                    |
| --------------  | ------------------------------------------- | -------------------------------------------------------------- |
| witan.app      | https://github.com/mastodonc/witan.app       | Web server and API                                             |
| witan.ui       | https://github.com/mastodonc/witan.ui        | Front end                                                      |
| witan.r.models | https://github.com/mastodonc/witan.r.models  | Mathematical models used in creating forecasts and projections |

This document will describe the minimum requirements for deploying a local version of Witan. It is not recommended that you use this set-up for production - for advice and guidance regarding production deployments, please [contact us](theteam@mastodonc.com).  

---------------------
## Prerequisites
A UNIX-based operating system such as Ubuntu is required. We recommend [Ubuntu Server 14.04.3 (64 bit)](http://www.ubuntu.com/download/server/thank-you?country=GB&version=14.04.3&architecture=amd64). These installation instructions have been tested on this distribution.  
Ensure your package manager is up-to-date: 
```
sudo apt-get update
```

#### Java
As we'll be handling source code we need to install the Java Development Kit (JDK). In this case, OpenJDK 7.
```
sudo apt-get install openjdk-7-jdk
```

#### Leiningen
Leiningen is the tool we use to manage our source code and build Clojure projects. Install it using [these instructions](http://leiningen.org/).

#### Cassandra
Cassandra is the database we use to hold all of the application's data. Install it using [these instructions](http://docs.datastax.com/en/cassandra/2.0/cassandra/install/installDeb_t.html) (for Debian-based OSs).

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
A UNIX-based operating system such as Ubuntu is required. We recommend [Ubuntu Server 14.04.3 (64 bit)](http://www.ubuntu.com/download/server/thank-you?country=GB&version=14.04.3&architecture=amd64). These installation instructions have been tested on this distribution using GNU bash 4.3.11. Note that VMs will likely need at least 2GB memory.

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

#### Docker
We use Docker to isolate all the different microservices. Install is using [these instructions](https://docs.docker.com/engine/installation/).

#### Cassandra
Cassandra is the database we use to hold all of the application's data. Install it using [these instructions](http://docs.datastax.com/en/cassandra/2.0/cassandra/install/installDeb_t.html) (for Debian-based OSs).

#### AWS Configuration
Some of the files in Witan are stored on Amazon S3 and therefore you need an account set up and ready. Create environmental variables in the following fashion:
```
export WITAN_APP_AWS_KEY=<Your Amazon Key>
export WITAN_APP_AWS_SECRET=<Your Amazon Secret>
```

You will also need a bucket called `witan-test-data` (if you use a different name you need to change the configuration for `witan.app`).

## Installing & Compiling

#### Obtain source code
Download the source code for each of the projects from GitHub - this will require you to have [public SSH keys configured](https://help.github.com/articles/generating-ssh-keys/) *and* access to the `witan.r.models` repository ([contact us](theteam@mastodonc.com)).

```
sudo apt-get install git
git clone git@github.com:MastodonC/witan.app.git
git clone git@github.com:MastodonC/witan.ui.git
git clone git@github.com:MastodonC/witan.r.models.git
```

#### Prepare code for use
##### witan.r.models
The code which runs the models needs to be installed into a system-wide path so that the other services can access it. First capture the version of the models we require:
```
grep -Eo "witan.models \"([0-9+]\.[0-9+]\.[0-9+])\"" witan.app/project.clj | cut -d\" -f2 > model_number
```
Then checkout the correct tag and install it.
```
cd witan.r.models
echo tags/$(cat ../model_number) | xargs git checkout
lein install
cd ..
```

##### witan.app
This application handles communication to the database and provides an API.
```
grep -Eo "witan.models \"([0-9+]\.[0-9+]\.[0-9+])\"" witan.app/project.clj | cut -d\" -f2 > model_number
```
Then checkout the correct tag, compile it and package it.
```
cd witan.app
lein uberjar
./prepare-aws-creds
sudo docker build -t witan.app .
cd ..
```
Note we're using the `dev.witan-app.edn` configuration because our Cassandra is local.

##### witan.ui
This application provides the front end to Witan, using web technology. First we compile it, then we package it:
```
cd witan.ui
./build_prod.sh
sudo docker build -t witan.ui .
cd ..
```


--net=host <<<< --- witan.app run ?? doesn't work

## Running

Start witan.ui:
```
sudo docker run -d -p 80:80 -e NGINX_SERVER_ADDR=localhost witan.ui
```

The application is now running. You should see output that looks similar to this:

```
Starting JettyServer
14:52:09.698 INFO  o.e.j.s.Server  - jetty-7.x.y-SNAPSHOT
14:52:09.726 INFO  o.e.j.s.AbstractConnector  - Started SelectChannelConnector@0.0.0.0:3000
```
AWS creds?

# Installing Witan
Witan is almost entirely written in [Clojure](http://clojure.org/) and [ClojureScript](http://clojure.org/clojurescript). It uses a '[microservices](https://en.wikipedia.org/wiki/Microservices)' architecture which means there are several applications in orbit that contribute to the overall operation:

| Service        | Location                                     | Description                                                    |
| --------------  | ------------------------------------------- | -------------------------------------------------------------- |
| witan.app      | https://github.com/mastodonc/witan.app       | Web server and API                                             |
| witan.ui       | https://github.com/mastodonc/witan.ui        | Front end                                                      |
| witan.r.models | https://github.com/mastodonc/witan.r.models  | Mathematical models used in creating forecasts and projections |

This document will describe the minimum requirements for deploying a local version of Witan. It is not recommended that you use this set-up for production - for advice and guidance regarding production deployments, please [contact us](witan@mastodonc.com).  

---------------------
## Prerequisites
A UNIX-based operating system such as Ubuntu is required. We recommend [Ubuntu Server 15.04.3 (64 bit)](http://www.ubuntu.com/download/server/thank-you?country=GB&version=15.04.3&architecture=amd64). These installation instructions have been tested on this distribution with GNU bash 4.3.30. Note that VMs will likely need at least 3GB memory.

Ensure your package manager is up-to-date: 
```bash
sudo apt-get update
```

#### Java (v1.8)
As we'll be handling source code we need to install the Java Development Kit (JDK).
```bash
sudo apt-get install openjdk-8-jdk -y
```

#### Leiningen (v2.x)
Leiningen is the tool we use to manage our source code and build Clojure projects. Install it using [these instructions](http://leiningen.org/). Run `lein` to confirm the install worked.

#### Docker (v1.9)
We use Docker to isolate all the different microservices. Install it using [these instructions](https://docs.docker.com/engine/installation/). Run `sudo docker ps` to confirm the install worked and the service is running.

#### Cassandra (v2.2.1)
Cassandra is the database we use to hold all of the application's data. Install it using [these instructions](http://docs.datastax.com/en/cassandra/2.0/cassandra/install/installDeb_t.html) (for Debian-based OSs). Run `cqlsh` to confirm the install worked and the service is running.

#### AWS Configuration
Some of the files in Witan are stored on Amazon S3 and therefore you need an account set up and ready. Create environmental variables in the following fashion:
```bash
export WITAN_APP_AWS_KEY=<Your Amazon Key>
export WITAN_APP_AWS_SECRET=<Your Amazon Secret>
```

You will also need a bucket called `witan-test-data` (if you use a different name you need to change the configuration for `witan.app`).

## Installing & Compiling

#### Obtain source code
Download the source code for each of the projects from GitHub - this will require you to have [public SSH keys configured](https://help.github.com/articles/generating-ssh-keys/) *and* access to the `witan.r.models` repository ([contact us](witan@mastodonc.com)).

```bash
sudo apt-get install git -y
git clone git@github.com:MastodonC/witan.app.git
git clone git@github.com:MastodonC/witan.ui.git
git clone git@github.com:MastodonC/witan.r.models.git
```

#### Prepare code for use
##### witan.r.models
The code which runs the models needs to be installed into a system-wide path so that the other services can access it. First capture the version of the models we require:
```bash
grep -Eo "witan.models \"([0-9+]\.[0-9+]\.[0-9+])\"" witan.app/project.clj | cut -d\" -f2 > model_number
```

Then checkout the correct tag and install it.
```bash
cd witan.r.models
echo tags/$(cat ../model_number) | xargs git checkout
lein install
cd ..
rm model_number
```

##### witan.app
This application handles communication to the database and provides an API. Prepare it with the following commands (ignore the error "Cannot drop non existing keyspace 'witan'").
```bash
cd witan.app
./prepare-aws-creds
lein uberjar
cd ..
```
To load the cassandra schema, in the clojure repl:
```clojure
(require '[db-setup :refer [load-db-schema!]])
(require '[witan.app.config :as c])
(load-db-schema! c/config)
```

##### witan.ui
This application provides the front end to Witan, using web technology. First we compile it, then we package it:
```bash
cd witan.ui
export WITAN_API_URL=http://localhost:3000
./build_prod.sh
sudo docker build -t witan.ui .
cd ..
```

## Running

Start witan.app:
```bash
java -jar witan.app/target/witan-app.jar &
```

Wait for a log line to be shown, similar to:
```bash
13:22:36.317 INFO  o.e.j.s.Server  - jetty-7.x.y-SNAPSHOT
13:22:36.364 INFO  o.e.j.s.AbstractConnector  - Started SelectChannelConnector@0.0.0.0:3000
```

Start witan.ui:
```bash
sudo docker run -d -p 80:80 -e NGINX_SERVER_ADDR=localhost witan.ui
```
Visit `http://localhost` in your browser and you should see the Witan login page.

## Adding a user

Before you can use Witan you'll need to add a user. First access the Witan console (a Clojure REPL):
```bash
lein repl :connect 5001
```
Once connected, type the following commands, replacing the name, email (username) and password for something appropriate:
```clojure
(in-ns 'witan.app.user)
(add-user! {:name "Test User" :username "foo@bar.com" :password "secret"})
(exit)
```

Now you can login using the username and password.


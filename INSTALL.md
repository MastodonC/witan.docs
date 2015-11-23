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
A UNIX-based operating system such as Ubuntu is required. We recommend [Ubuntu Server 14.04.3 (64 bit)](http://www.ubuntu.com/download/server/thank-you?country=GB&version=14.04.3&architecture=amd64). These installation instructions have been tested on this distribution using GNU bash 4.3.11.  
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

## Installing

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
Then checkout the correct tag and install it.
```
cd witan.app
lein uberjar
```
AWS creds?

# Codetask Parser for the HTWG-Konstanz learning platform
A Scala Parser using "Scala Parser Combinators" for the HTWG-Konstanz learning platform **codetask**.

### Build status: [![Build Status](https://travis-ci.com/artur-ott/codetask-parser.svg?branch=master)](https://travis-ci.com/artur-ott/codetask-parser)

## Development
For the development you will need the docker-compose tool.
To start the server in development mode, you have to start the docker container:
``` bash
docker-compose up -d
```
It will start a *play* and a *ngrok* container. Where play is the Play Framework server and ngrok is the server where Ngrok service is running. (Ngrok is used to link your local server to the internet, so github is able to send events to your local server)

To run the play server follow the commands:
``` bash
# start bash in the play container
docker-compose exec play bash

# start local server
sbt run
```

## Deployment

### Building the parser library
``` bash
# change directory
cd lib

# packaging the library
sbt package
```
After the commands have been executed, a library **jar** file is located in the subdirectory *target/scala-x.y* (where x.y is the Scala version).

### Building the automation server
First, copy the previously generated **parser library jar** file into the *lib* (`project/lib`) directory of the *project*.

The following commands should then be executed:
``` bash
# change directory
cd project

# build production version
sbt dist
```
After the commands have been executed, a build **zip** file for production is located in the subdirectory *target/universal*.

### Running on the server
You can now copy the zip file to the desired server and unpack it.
After the zip-file has been unpacked, the server can be started:
``` bash
# change directory
cd bin

# start server
./codetask-parser
```

### Production configuration
Create a configuration file e.g *prod.conf*.
The following configuration must be made:
```
include "application.conf"

play.http.secret.key=...
users.key=...
firebase.database="..."
github.key=...
github.clientId=...
github.clientSecret=...
```
Where play.http.secret.key *Play Framework* key to secure the server.
User and Github keys are the keys useed to access the user and github links on the webserver.
Firebase is the link to the database.
Github client id and client secret are the oauth keys from github. These are used for exceeding the limit of the requests.

After that you can start the server with the created configuration as follows:
``` bash
# start server with the created configuration
./codetask-parser -Dconfig.file="<path to the conf file>/prod.conf"
```
**IMPORTANT:** If the server was previously closed by a *kill*, the *RUNNING_PID* file in the root directory must be deleted.

### Github-Webhook
Now you can set up the webhook for the desired repository.
To do this, go to Settings in the repository and select Webhooks.
Click on Add Webhook in the upper right corner, enter Url and set content type to json.
At the end click on Add Webhook to create it. 

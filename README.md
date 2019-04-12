# Docker skeleton
docker skeleton for creating a controlled environment for php applications

# Prerequisites
- 64 bit operating system
- Docker (https://www.docker.com/products/docker)
- A pack of cigarettes

# Extra stack

For a code editor use either:

1. phpStorm
2. Visual Studio Code

Handy stuff for developing:

1. Robo 3T for MongoDB
2. Sequel Pro for MySQL management
3. Tower or SourceTree for GIT

Other handy tools on Mac:

1. Spectacle (for window snapping)
2. Lightshot screenshot create easy screenshows
3. Charles for Proxy (testing OAuth flows)
4. Dash for documentation PHP / Yii2
5. 1Password for password management

# Integration with phpStorm

It's handy to start processes like cron/jobs straight from phpStorm. This is really easy if you use a very good IDE like phpStorm.

To do this follow these steps:

1. Go to preferences
2. Go to languages / frameworks > PHP > and click the three ... next to CLI interpreter
3. Click the plus button and select: "From Docker, Vagrant, VM, remote"
4. Select Docker Compose and select your docker-compose.yml file. Also select the console service
5. It will check the settings and connection to Docker. **Make sure to select ""Connect to existing container (docker-compose exec)**
6. Close the settings page and in the right top click "Edit configurations"
7. Add a new PHP script and point the path to the full path to your /yii file, ie. /Users/alex/Docker/data/databrydge_aws/yii
8. Set arguments for instance to cron/jobs
9. Click apply, you can run now and it will run inside the container directly. Keyboard shortcuts are ⌃(ctrl) + D for debug mode and ⌃(ctrl) + D for regular run mode

Added bonus is that you also have a nice debug button now (if you implemented XDebug) You can either run it in debug mode and then it will automatically hit the breakpoints without you clicking the listen button.

# XDebug

XDebug is only installed into the console container. Since most of the work is done there. It actually works by opening up a port on your host machine (we use 10000 to prevent conflicts with PHP FPM if we want to use web container too). It then listens for incoming connections from the console container. 

## Steps for phpStorm

### 1. Configure XDebug in phpStorm

Go to preferences, languages and frameworks, php, debug and enable XDebug and specify the correct port (10000)

<img width="600" alt="" src="https://user-images.githubusercontent.com/1567497/56058703-625dbf80-5d62-11e9-991d-0426a90dd187.png">

### 2. Setup paths

Now we need to setup a server under the same menu ("Servers"). This allows phpStorm to link the files in the container to the execution paths in the project.

Setup a server like this:


<img width="600" alt="" src="https://user-images.githubusercontent.com/1567497/56058784-b49ee080-5d62-11e9-8fdb-0fda67813ddf.png">


Now phpStorm nows when a file is being executed, the path is sent back remotely and mapped by phpStorm to a local file in your machine.

### 3. Run a debug session

First click the listener icon to allow phpStorm to allow listening for debug connections on port 10000:

<img width="123" alt="" src="https://user-images.githubusercontent.com/1567497/56058915-152e1d80-5d63-11e9-8ab9-be5a5ceda13f.png">

Don't forget to set a breakpoint in the code.

You can now execute the php script with the professional way:

`docker-compose exec console /databrydge/yii cron/jobs`

And the script should stop at the breakpoint.

## Visual Studio Code
Instructions for Visual Studio Code should be similar. The real significant step is to make sure the paths on your local machine are mapped to the paths inside the docker containers. phpStorm does this by using the "servers" setting.

You should install [this plugin](https://github.com/felixfbecker/vscode-php-debug) anyway for VisualCode (update these steps with pictures when you are setting up for VisualCode please!). The variable you should look at in this case is pathMappings.

# Usage
1. Download or checkout this repository.
2. Open a terminal and navigate to the skeleton.
3. run `docker-compose up --build` or `docker-compose up -d` to run in background after first build
4. You need to add the following entries to your hosts file:
   - 127.0.0.1 databrydge.test
   - 127.0.0.1 dataswitcher.test
5. After docker completed downloading all images, the services will run
6. Download or git clone databrydge_aws and/or dataswitcher_aws in the data directory.
7. Run `composer install` within the console container, start with: `docker-compose exec console sh`, then use `cd databrydge`
8. Copy the .env.example to .env 
9. If you want to run commands in the app container use command `docker-compose exec console sh` to open a terminal open within the docker container. Then use `cd` to switch to the working project folder. From there run commands as if on a local environment. If you are on the next level of Docker you can also use: `docker-compose exec console /databrydge/yii cron/jobs` to execute directly in the container without invoking a shell 
10. To connect to mysql within the application use as ip address `dbg_mariadb`, for mongodb use `dbg_mongodb`
11. To connect to mysql / mongodb from outside the docker application use `127.0.0.1` 

# Configuration

## Database
The database settings can be changed in docker-compose.yml

## Webserver (php-apache)
You can change the php configuration in the services/php-apache/config/ folder.
Any .ini files you put here, will be loaded in php.
After changing any of the ini files, you'll need to rebuild. `docker-compose up --build`

# Default configuration
The default configuration for the database is as follows:

- hostname: db
- root password: root
- user: db
- password: db
- database: db

# Ports
Docker compose will start the following services
You can access these services from a browser.
For example:
http://127.0.0.1:80 for the webserver

1. The webserver (port: 80)
2. Phpmyadmin (port: 8181)


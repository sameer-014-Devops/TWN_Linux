</details>

******

<details>
<summary>Exercise 1: Linux Mint Virtual Machine </summary>
 <br />

Download Page
- https://linuxmint.com/download.php

Installation Guide
- https://linuxmint-installation-guide.readthedocs.io/en/latest/

</details>

******

<details>
<summary>Exercise 2: Bash Script - Install Java </summary>
 <br />

**script:**
```sh
#!/bin/bash

apt update
apt install -y default-jre

java_version=$(java -version 2>&1 >/dev/null | grep "java version\|openjdk version" | awk '{print substr($3,2,2)}')

if [ "$java_version" -ge 11 ]
then
    echo Java version 11 or greater installed successfully
else
    echo Java installation failed
fi
```

Execute script with sudo 
</details>

******

<details>
<summary>Exercise 3, 4, 5: Bash Script - User Processes Sorted </summary>
 <br />

**script  :**
```sh
#!/bin/bash

echo -n "Would you like to sort the processes output by memory or CPU? (m/c) "
read sortby
echo -n "How many results do you want to display? "
read lines

if [ "$sortby" = "m" ]
then
    ps aux --sort -rss | grep -i `whoami` | head -n "$lines"
elif [ "$sortby" = "c" ]
then
    ps aux --sort -%cpu | grep -i `whoami` | head -n "$lines"
else
    echo "No input provided. Exiting"
fi
```

</details>

******

<details>
<summary>Exercise 6, 7: Start NodeJs App </summary>
 <br />

**script**
```sh
#!/bin/bash

# prepare environment, install all tools
apt update

echo "install node, npm, curl, wget, net-tools"
apt install -y nodejs npm curl net-tools  
sleep 15
echo ""
echo "################"
echo ""

# read user input for log directory
echo -n "Set log directory location for the application (absolute path): "
read LOG_DIRECTORY
if [ -d $LOG_DIRECTORY ]
then
  echo "$LOG_DIRECTORY already exists"
else
  mkdir -p $LOG_DIRECTORY
  echo "A new directory $LOG_DIRECTORY has been created"
fi

# display nodeJS version
node_version=$(node --version)
echo "NodeJS version $node_version installed"

# display npm version
npm_version=$(npm --version)
echo "NPM version $npm_version installed"

echo ""
echo "################"
echo ""

# fetch NodeJS project archive from s3 bucket
wget https://node-envvars-artifact.s3.eu-west-2.amazonaws.com/bootcamp-node-envvars-project-1.0.0.tgz

# extract the project archive to ./package folder
tar zxvf ./bootcamp-node-envvars-project-1.0.0.tgz

# set all needed environment variables
export APP_ENV=dev
export DB_PWD=mysecret
export DB_USER=myuser 

# change to package directory
cd package

# install application dependencies
npm install

# start the nodejs application in the background
node server.js &

# display that nodejs process is running
ps aux | grep node | grep -v grep

# display that nodejs is running on port 3000
netstat -ltnp | grep :3000
```

</details>

******

<details>
<summary>Exercise 8, 9: Start NodeJs App with Service user & Log Directory </summary>
 <br />

**script**
```sh
#!/bin/bash

# prepare environment, install all tools
apt update
NEW_USER=myapp

echo "install node, npm, curl, wget, net-tools"
apt install -y nodejs npm curl net-tools  
sleep 15
echo ""
echo "################"
echo ""

# read user input for log directory
echo -n "Set log directory location for the application (absolute path): "
read LOG_DIRECTORY
if [ -d $LOG_DIRECTORY ]
then
  echo "$LOG_DIRECTORY already exists"
else
  mkdir -p $LOG_DIRECTORY
  echo "A new directory $LOG_DIRECTORY has been created"
fi

# display nodeJS version
node_version=$(node --version)
echo "NodeJS version $node_version installed"

# display npm version
npm_version=$(npm --version)
echo "NPM version $npm_version installed"

echo ""
echo "################"
echo ""

# create new user to run the application and make owner of log dir
useradd $NEW_USER -m
chown $NEW_USER -R $LOG_DIRECTORY

# executing the following commands as new user using 'runuser' command

# fetch NodeJS project archive from s3 bucket
runuser -l $NEW_USER -c "wget https://node-envvars-artifact.s3.eu-west-2.amazonaws.com/bootcamp-node-envvars-project-1.0.0.tgz"

# extract the project archive to ./package folder
runuser -l $NEW_USER -c "tar zxvf ./bootcamp-node-envvars-project-1.0.0.tgz"

# start the nodejs application in the background, with all needed env vars with new user myapp
runuser -l $NEW_USER -c "
    export APP_ENV=dev && 
    export DB_PWD=mysecret && 
    export DB_USER=myuser && 
    export LOG_DIR=$LOG_DIRECTORY && 
    cd package && 
    npm install && 
    node server.js &"

# display that nodejs process is running
ps aux | grep node | grep -v grep

# display that nodejs is running on port 3000
netstat -ltnp | grep :3000
```

Execute script with sudo command

</details>

******

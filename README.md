How to start ?

Download and extract zip:
$ curl -LO https://github.com/xebialabs/devops-as-code-workshop/archive/master.zip
$ unzip master.zip
$ cd devops-with-xlr-xld


Start up the XL DevOps Platform

1. If you are running the workshop on Windows, execute the following command to be able to run the Docker Compose file:

> set COMPOSE_CONVERT_WINDOWS_PATHS=1

2. Start up the XL DevOps Platform:
$ docker-compose up --build

3. Wait for XL Deploy and XL Release to have started up. 
Open the XL Deploy GUI at http://localhost:4516/ and login with the username admin and password admin. 
Open the XL Release GUI at http://localhost:5516/ and login with the username admin and password admin.


Install the XL CLI

Open a new terminal window and install the XL command line client:

Mac
$ curl -LO https://s3.amazonaws.com/xl-cli/bin/8.6.1/darwin-amd64/xl
$ chmod +x xl
$ sudo mv xl /usr/local/bin

Linux
$ curl -LO https://s3.amazonaws.com/xl-cli/bin/8.6.1/linux-amd64/xl
$ chmod +x xl
$ sudo mv xl /usr/local/bin

Windows
Download https://s3.amazonaws.com/xl-cli/bin/8.6.1/windows-amd64/xl.exe and place it somewhere on your %PATH%
Verify that you have installed the correct version of the XL CLI by executing the following command:
$ xl version



Deploying a Docker application

Exercise 1: Review the XL DevOps Platform running on Docker

When the XL DevOps Platform was started up by the Docker Compose file, four containers were started:

xl-deploy runs XL Deploy.
xl-release runs XL Release.
dockerproxy is where XL Deploy will deploy to. It's a proxy for the Docker engine on your local machine, the same that runs XL Release and XL Deploy..
xl-cli runs the XL CLI to apply the configure-xl-devops-platform.yaml YAML file. This XL YAML file adds two configurations:
It adds an XL Deploy configuration to XL Release so that the latter can find the former.
It adds a docker.Engine configuration to XL Deploy so that XL Deploy can deploy to the Docker engine (via the Docker proxy).



Exercise 2: Set up the environment


Now that we have the XL DevOps Platform up and running and connected to our local Docker instance, let's create an environment that contains it.

Open a new terminal window and go to the directory where you unzipped the devops-with-xlr-xld repository.

Create the environment that contains the Docker engine by applying its XL YAML file:

$ xl apply -f exercise-2/docker-environment.yaml
Open the XL Deploy GUI and review the environment that you just created.



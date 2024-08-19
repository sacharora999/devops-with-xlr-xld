# DevOps with xlr and xld


# How to start ?

## Download the code

1) Download and extract the workshop zip:
```
$ curl -LO https://github.com/sacharora999/devops-with-xlr-xld/archive/refs/heads/main.zip
$ unzip main.zip
$ cd devops-with-xlr-xld
```


## Start up the XL DevOps Platform

1) If you are already running XL Deploy or XL Release on your local machine, please stop them.

2) If you are running the workshop on Windows, execute the following command to be able to run the Docker Compose file:

```
> set COMPOSE_CONVERT_WINDOWS_PATHS=1
```

For more information on this environment variable, read [the documentation for Docker Compose](https://docs.docker.com/compose/reference/envvars/#compose_convert_windows_paths)

3) Start up the XL DevOps Platform:

```
$ docker-compose up --build
```

4) Wait for XL Deploy and XL Release to have started up. 

5) Open the XL Deploy GUI at http://localhost:4516/ and login with the username `admin` and password `admin`. 
6) Open the XL Release GUI at http://localhost:5516/ and login with the username `admin` and password `admin`. 

## Install the XL CLI

1) Open a new terminal window and install the XL command line client:

### Mac
```
$ curl -LO https://s3.amazonaws.com/xl-cli/bin/8.6.1/darwin-amd64/xl
$ chmod +x xl
$ sudo mv xl /usr/local/bin
```

### Linux
```
$ curl -LO https://s3.amazonaws.com/xl-cli/bin/8.6.1/linux-amd64/xl
$ chmod +x xl
$ sudo mv xl /usr/local/bin
```

### Windows
Download https://s3.amazonaws.com/xl-cli/bin/8.6.1/windows-amd64/xl.exe
and place it somewhere on your `%PATH%`

2) Verify that you have installed the correct version of the XL CLI by executing the following command:

```
$ xl version
```

The output should look like this:
```
CLI version:             8.6.1
Git version:             8.6.0-4-gaf96240
API version XL Deploy:   xl-deploy/v1
API version XL Release:  xl-release/v1
Git commit:              af962402e3b0d9a6cfb7c366e35f21578e848f05
Build date:              2019-03-27T13:07:32.971Z
GO version:              go1.11
OS/Arch:                 darwin/amd64
```

The last line will be different, depending on the architecture of your machine.

3) Test the XL CLI by executing the following command:
```
$ xl help
```

The output should look  like this:
```
XL Cli 8.6.1
The xl command line tool provides a fast and straightforward method for provisioning
XL Release and XL Deploy with YAML files. The files can include items like
releases, pipelines, applications and target environments.

Usage:
  xl [command]

Available Commands:
  apply       Apply configuration changes
  blueprint   Create a Blueprint
  generate    Generate configuration
  help        Help about any command
  ide         IDE commands
  license     Display license info
  preview     Preview Deployment
  version     Display version info
  wrapper     Generate XL wrapper

Flags:
      --blueprint-current-repository string   Current active blueprint repository name
      --config string                         config file (default: $HOME/.xebialabs/config.yaml)
  -h, --help                                  help for xl
  -q, --quiet                                 suppress all output, except for errors
  -v, --verbose                               verbose output
      --xl-deploy-authmethod string           Authentication method to access the XL Deploy server (default "http")
      --xl-deploy-password string             Password to access the XL Deploy server (default "admin")
      --xl-deploy-url string                  URL to access the XL Deploy server (default "http://localhost:4516/")
      --xl-deploy-username string             Username to access the XL Deploy server (default "admin")
      --xl-release-authmethod string          Authentication method to access the XL Release server (default "http")
      --xl-release-password string            Password to access the XL Release server (default "admin")
      --xl-release-url string                 URL to access the XL Release server (default "http://localhost:5516/")
      --xl-release-username string            Username to access the XL Release server (default "admin")

Use "xl [command] --help" for more information about a command.
```

# Part I - Deploying a Docker application

## Exercise 1: Review the XL DevOps Platform running on Docker

When the XL DevOps Platform was started up by the Docker Compose file https://github.com/sacharora999/devops-with-xlr-xld/blob/main/docker-compose.yaml , four containers were started:
* `xl-deploy` runs XL Deploy.
* `xl-release` runs XL Release.
* `dockerproxy` is where XL Deploy will deploy to. It's a proxy for the Docker engine on your local machine, the same that runs XL Release and XL Deploy..
* `xl-cli` runs the XL CLI to apply the [`configure-xl-devops-platform.yaml`](https://github.com/sacharora999/devops-with-xlr-xld/blob/main/docker/xl-cli/configure-xl-devops-platform.yaml) YAML file. This XL YAML file adds two configurations:
  * It adds an XL Deploy configuration to XL Release so that the latter can find the former.
  * It adds a `docker.Engine` configuration to XL Deploy so that XL Deploy can deploy to the Docker engine (via the Docker proxy).

1) Open the XL Deploy GUI, find the **local-docker** entry in the Infrastructure tree and run the **Check Connection** control task.


## Exercise 2: Set up the environment

Now that we have the XL DevOps Platform up and running and connected to our local Docker instance, let's create an environment that contains it.

1) Open a new terminal window and go to the directory where you unzipped the `devops-with-xlr-xld` repository.

2) Create the environment that contains the Docker engine by applying its XL YAML file:
```
$ xl apply -f exercise-2/docker-environment.yaml
```
3) Open the XL Deploy GUI and review the environment that you just created.

## Exercise 3: Import a simple package and deploy it

Let's try and deploy something to our local Docker instance with XL Deploy. We'll start with a single container; the backend part of the REST-o-rant application. It's called **rest-o-rant-api** because it serves up the API.

1) Import the REST-o-rant-api package:

```
$ xl apply -f exercise-3/rest-o-rant-api-docker.yaml
```
![image](https://github.com/user-attachments/assets/f3241397-fdea-46d5-a247-deaffa788b12)

2) Open the XL Deploy GUI and review version **1.0** of the **rest-o-rant-api-docker** application. Compare it with the contents of the XL YAML file that you applied in the previous step.

3) Using the GUI, deploy version **1.0** of the **rest-o-rant-api-docker** application to the **Local Docker Engine** environment.


![image](https://github.com/user-attachments/assets/da5c8b95-ed76-46c7-804d-e167d08b476d)


![image](https://github.com/user-attachments/assets/2fe69bc5-f58f-472b-8d75-5e2d715da5c3)


![image](https://github.com/user-attachments/assets/1b294ff1-caaa-4001-bb9e-f3239a3aa719)




4) When the deployment has finished, check whether the **rest-o-rant-api** container is running:
```
$ docker ps
```
![image](https://github.com/user-attachments/assets/134a485a-03b9-42dd-82d9-58ae8ba93210)

The output should look something like this:
```
$ docker ps
CONTAINER ID        IMAGE                                           COMMAND                  CREATED             STATUS              PORTS                    NAMES
b0611ff9ffbb        xebialabsunsupported/rest-o-rant-api            "java -Djava.securit…"   11 seconds ago      Up 10 seconds       8080/tcp                 rest-o-rant-api
f80c2b00a88e        xebialabsunsupported/xl-release:8.6.1           "/opt/xebialabs/tini…"   3 days ago          Up About an hour    0.0.0.0:5516->5516/tcp   devops-as-code-workshop_xl-release_1
a99c31ddd458        tecnativa/docker-socket-proxy:latest            "/docker-entrypoint.…"   3 days ago          Up About an hour    2375/tcp                 devops-as-code-workshop_dockerproxy_1
68a5c6439540        xebialabsunsupported/xl-deploy:8.6.1            "/opt/xebialabs/tini…"   3 days ago          Up About an hour    0.0.0.0:4516->4516/tcp   devops-as-code-workshop_xl-deploy_1
```

## Exercise 4: Deploy a (slightly) more complex package

Serving a REST API is all nice and dandy, but it's pretty useless without a UI. So let's deploy the **rest-o-rant-web** container. Because it needs to access the **rest-o-rant-api** application to get its data, we'll also need to define a Docker network to allow the two containers to communicate. Version **1.1** of the **rest-o-rant-api** application will define that network.

1) Import the new version of the **rest-o-rant-api-docker** package, as well as the **rest-o-rant-web-docker** package:
```
$ xl apply -f exercise-4/rest-o-rant-docker.yaml
```

2) Open the XL Deploy GUI and review the two packages that you just imported and compare them with the XL YAML file. Notice the net **rest-o-rant-network** deployable in the **rest-o-rant-api-docker** package.

3) Using the GUI, deploy version **1.1** of the **rest-o-rant-api-docker** package to the **Local Docker Engine** environment.

   ![image](https://github.com/user-attachments/assets/af89654b-58c1-4d3a-b823-f78acb5d48b6)


5) Using the GUI, deploy version **1.0** of the **rest-o-rant-web-docker** package to the **Local Docker Engine** environment.

![image](https://github.com/user-attachments/assets/612ea9c2-9d36-4c6c-8238-285d56a92f28)

![image](https://github.com/user-attachments/assets/0d636610-b0a2-429a-bcd3-074e09a3a6c9)



6) When the deployment has finished, open a new browser tab and access it at http://localhost:8181/. You should see a text saying "Find the best restaurants near you!".
Type "Cow" in the search bar and click "Search" to find the "Old Red Cow" restaurant.

![image](https://github.com/user-attachments/assets/a99f2e8d-8202-4a3f-a5a2-916c5ac58063)

![image](https://github.com/user-attachments/assets/cc116993-a81b-4fe5-99de-3dec9e7bfdc1)



## Exercise 5: Import a pipeline

OK, we've deployed the application, but how do we get rid of it? Well, let's do that manually for one last time:

1) Undeploy the **rest-o-rant-web** application from the **Local Docker Engine** environment.

2) Undeploy the **rest-o-rant-api** application from the **Local Docker Engine** environment.



But let's make sure that you don't forget next time that you run this workshop. Let's create a simple pipeline.

3) Import that REST-o-rant pipeline:
```
$ xl apply -f exercise-5/rest-o-rant-docker-pipeline.yaml
```

Make sure to update IP Addr of XLD Server in below template

![image](https://github.com/user-attachments/assets/ab6ae587-7aab-4449-8782-42878a14c5b2)


![image](https://github.com/user-attachments/assets/665885a1-d143-4a0b-9f5d-400d53de6501)


4) Open the XL Release GUI, go to **Design** tab, click on the **REST-o-rant** folder and then go to the **Templates** tab. Review the **REST-o-rant on Docker** pipeline that you've just imported and compare it to the XL YAML file.

5) Start a new release from that template and follow the instructions.

![image](https://github.com/user-attachments/assets/7d836c28-86e0-4798-85ca-f55b8cbc860b)




# Part II - AWS (ECS and Fargate)

In Part II of the workshop we will show you how to use the XL DevOps Platform to provision container infrastructure into AWS with CloudFormation and then deploy a simple monolith application into it.

For this part of the workshop, you'll need access to an AWS account with administrator privileges and you'll need to have Python installed on your machine.

## Exercise 6: Import AWS credentials into XL Deploy

Let's start by connecting XL Deploy with your AWS account.

1) Install the AWS CLI as described here:
https://docs.aws.amazon.com/cli/latest/userguide/installing.html

2) Configure the AWS CLI as described here and select region eu-west-1 (Ireland):
https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-quick-configuration

    **N.B.:** If you have already been using the AWS CLI and have multiple profiles, please ensure the default profile is configured correctly.

3) If you do not have Python installed on your machine, install it.

4) Run the provided Python script to generate an XL YAML file with your credentials:
```
$ ./exercise-6/awsconfig2xld.py > exercise-6/AWSConfig.yaml
```

![image](https://github.com/user-attachments/assets/441d5631-dda5-4c9a-a297-b572c7a5152c)


5) Review the generate XL YAML file.

![image](https://github.com/user-attachments/assets/e4c33c95-d51a-4626-83cd-aae3d1d2f475)


6) Apply the generated XL YAML file:
```
$ xl apply -f exercise-6/AWSConfig.yaml
```
![image](https://github.com/user-attachments/assets/9c1463d5-3a0e-4bda-8f94-9fd8efe22fe4)


7) In XL Deploy, navigate to the **Infrastructure/aws** object in the Explorer tree and execute the "Check connection" task to verify that the connection could be made.


![image](https://github.com/user-attachments/assets/9b634e13-396e-4d05-b6e9-81f81a9e3703)

![image](https://github.com/user-attachments/assets/217751ae-62cc-4edd-bd19-bfaf36e6eeab)


## Exercise 7: Create AWS infrastructure with CloudFormation

Now privision the infrastructure using XL Deploy and CloudFormation. We'll let XL Deploy manage and run the CloudFormation template for us and let CloudFormation create the actual resources.

1) Import the CloudFormation template and its metadata into XL Deploy:
```
xl apply -f exercise-7/ecommerce-infrastructure.yaml 
```
![image](https://github.com/user-attachments/assets/8b9c41d9-20e1-4fd1-bcc3-2bdaa3b84f5d)

2) In XL Deploy, deploy version **1.0** of the **ecommerce-infrastructure** package to the **AWS** environment.

   ![image](https://github.com/user-attachments/assets/941665df-a62e-4864-8b4d-28fa354ba1b1)


3) When the deployment has finished, go to the AWS Console, navigate to the CloudFormation service, and review the **ecommerce-cloudformation** stack and the resources it has created:

![image](https://github.com/user-attachments/assets/be9abce7-f6e7-4988-ae1d-225a5e6362f5)
![image](https://github.com/user-attachments/assets/b1685814-2e82-4ad0-9066-5d55aa92c908)


    * A VPC with routes, subnets, etc.
    * IAM roles and policies
    * A load balancer with a target group
    * An ECS cluster


![image](https://github.com/user-attachments/assets/bb5806e1-ef2f-409d-9ca8-84ad46b383f0)


## Exercise 8: Deploy a monolith application 

Now that the infrastructure has been provisioned, let's deploy the applicaiton on top of it.

1) Import the e-commerce application into XL Deploy:
```
$ xl apply -f exercise-8/ecommerce-application.yaml 
```

2) Import the e-commerce pipeline into XL Release:
```
$ xl apply -f exercise-8/ecommerce-pipeline.yaml
```

3) In XL Release, create a new release based on the **e-Commerce CD pipeline** template in the **e-Commerce** folder. **Do not** start it yet!

4) We've already provisioned the infrastructure in the previous exercise. So, delete the first task of the first phase (**Provision e-commerce infrastructure**). In day-to-day use, one would let XL Release handle the complete orchestration, from provisioning the infrastructure to the very end when the infrastructure is deprovisioned.

5) Start the release.

6) When the release gets to the **Verify application manually** task, open the task and follow the instructions to complete it.

7) Wait until the release has finished.

## Exercise 9: Shutting down the XL DevOps Platform

1) Shut down the XL DevOps Platform:

```
$ docker-compose down
```

Not only will this stop the XL DevOps Platform, it will also remove any data stored on it. Therefore you should make sure that all releases and deployments have finished and that you've undeployed any applications you've deployed with it before you shut down the XL DevOps Platform.



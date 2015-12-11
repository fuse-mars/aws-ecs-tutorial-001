# AWS ECS Tutorial
This tutorial introduces you on how to run a gradle based project using the AWS ecs-cli tool.

Bullet points:
* Audience
* Define AWS ECS
* Use case
* [Step by step walkthrough](#step-by-step-walkthrough)
* Future work

## Audience
This tutorial is for people who knows how to use Docker, DOcker Compose, and write gradle based applications.
You should also be familiar with Amazon Webservices (AWS).

## Define AWS ECS
AWS EC2 Container Service (ECS) is a container management service that allows users to run containers in a scalable and secure way. For more info you visit [AWS website](https://aws.amazon.com/ecs/).

You interact with aws ecs, through the AWS web console or by using [ecs-cli](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI.html), the command line tool provided by Amazon.

## Use case


## Step by step walkthrough

We are going to use [an application](http://fuse-mars.github.io/spring-akka-command/) that allows you to save your Food spending. I used a "ubuntu 14.04 x86_64" machine for this tutorial.

* Installing ecs-cli
  
  Follow [the link from AWS](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI_installation.html) to install and configure ecs-cli.

* Getting the Application code
  ecs-cli uses docker-compose like syntax, so the basic knowledge of Docker Compose is expected. 
  There some exceptions though:
  * ecs-cli does not support "dockerfile" configuration value, so you always have to use "image" value.
  * 

You can use [my pre built docker image](https://hub.docker.com/r/fusemars/akkaspring_command/) of the application
```shell
docker pull fusemars/akkaspring_command
```
or download the code and build your own
```dockerfile
# Dockerfile content

FROM niaquinto/gradle
MAINTAINER Full Name <username@company.com>
WORKDIR /

RUN apt-get update
RUN apt-get dist-upgrade -y

RUN DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install python-software-properties
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install software-properties-common

RUN DEBIAN_FRONTEND=noninteractive apt-get install --yes --force-yes git

RUN git clone https://github.com/fuse-mars/spring-akka-command.git

RUN echo $(ls -al /)
EXPOSE 9090

CMD [" build bootRun"]

ENTRYPOINT gradle build bootRun -p spring-akka-command
```

```
docker build -t username/akkaspring_command -f Dockerfile . # replace "username" with your dockerhub username
```

* Setting up the docker-compose.yml file
```yml
# replace "username" with your dockerhub username
web:
  image: username/akkaspring_command
  cpu_shares: 100
  mem_limit: 1024288000
  ports:
   - "9090:9090"
  environment:
   - HOSTNAME=localhost

```
* Test locally
```shell
docker-compose --file docker-compose-use-image.yml up
```
After successiful run, you should be able to access your app at `<host-ip>:9090`
* Creating the cluster
```shell
export AWS_ACCESS_KEY_ID=<aws-id>
export AWS_SECRET_ACCESS_KEY=<aws-secret>
ecs-cli configure --region us-east-1 --access-key $AWS_ACCESS_KEY_ID --secret-key $AWS_SECRET_ACCESS_KEY --cluster food-spending #replace "food-spending" with your favorite name
ecs-cli up --keypair id_rsa --capability-iam --size 2 --instance-type t2.medium # replace "id_rsa" with the name of your existing aws keypair.
```
Here we have two important commands `ecs-cli configure` and `ecs-cli up`. The former configures your machine so that ecs-cli can connect to aws and access ec2 instances easily. The latter creates the cluster and adds instances to it.

The `ecs-cli up` allows to specify the security group `--security-group group_id`, which is good when you want to enable diffrent port for public access. howevwer, i could not get it to work **LEFT FOR TODO**

* Running the application
```shell
ecs-cli compose --file docker-compose.yml service up # replace "docker-compose.yml"  with the path to your docker-compose file
```
* Cleaning up everything
```
ecs-cli compose --file docker-compose.yml service rm
ecs-cli down --force
```

## Future work
* How do you assign domain names to instances of the ecs cluster?
* How to use cloud front with ecs?
* Is there a possibility to run multiple apps on the same cluster?


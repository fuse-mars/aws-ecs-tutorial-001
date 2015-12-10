# AWS ECS Tutorial
This tutorial introduces you on how to run a gradle based project using the AWS ecs-cli tool.

Bullet points:
* Audience
* Define AWS ECS
* Use case
* [Step by step walkthrough](#step-by-step-walkthrough) 

## Audience
This tutorial is for people who knows how to use Docker, DOcker Compose, and write gradle based applications.
You should also be familiar with Amazon Webservices (AWS).

## Define AWS ECS
AWS EC2 Container Service (ECS) is a container management service that allows users to run containers in a scalable and secure way. For more info you visit [AWS website](https://aws.amazon.com/ecs/).

You interact with aws ecs, through the AWS web console or by using [ecs-cli](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI.html), the command line tool provided by Amazon.

## Use case


## Step by step walkthrough

We are going to use [an application](http://fuse-mars.github.io/spring-akka-command/) that allows you to save your Food spending.

* Installing ecs-cli
  
  Follow [the link from AWS](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI_installation.html) to install and configure ecs-cli.

* Getting the Application code
  ecs-cli uses docker-compose like syntax, so the basic knowledge of Docker Compose is expected. 
  There some exceptions though:
  * ecs-cli does not support "build" configuration value, so you always have to use "image" value.
  * 

You can use [my pre built docker image](https://hub.docker.com/r/fusemars/akkaspring_query/) of the application
```
docker pull fusemars/akkaspring_query
```
or download the code and build your own
```
# Dockerfile content

FROM niaquinto/gradle
MAINTAINER Marcellin Nshimiyimana <nmarcellin2@gmail.com>
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
docker build -t username/akkaspring_query -f Dockerfile .
```

* Setting up the docker-compose.yml file
```yml

```
* Creating the cluster
* Running the application
* Cleaning up everything.




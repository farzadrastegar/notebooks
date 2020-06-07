## What is this all about?

This is Part 3 of a series about building microservices with Spring Cloud and Netflix OSS. See [this link](https://callistaenterprise.se/blogg/teknik/2015/04/27/building-microservices-part-3-secure-APIs-with-OAuth/) for further detail. For more info about OAuth 2.0, see this [tutorial](http://tutorials.jenkov.com/oauth2/index.html).

## How to run?

In order to run code related to Part 3, there are two options.
* Follow [the instructions](https://callistaenterprise.se/blogg/teknik/2015/04/27/building-microservices-part-3-secure-APIs-with-OAuth/)  and run code in your favorite environment.
* Use **Docker** (tested on [Play with Docker](https://labs.play-with-docker.com/)).

### Docker

The code snippets below run the Part 3 Github code using multiple Docker nodes through Ubuntu containers. These code snippets were tested on the [Play with Docker](https://labs.play-with-docker.com/) environment.

Note: Running code on Docker Swarm is NOT covered in Part 3.

##### For every node, run this first
```shell
# Run an Ubuntu container
docker run -it -d --name ubuntu16 --net=host ubuntu:16.04
docker exec -it ubuntu16 /bin/bash
apt update
apt install wget git -y

# Install Java 8
wget https://www.dropbox.com/s/u01js72eg57tjep/install-java8.bash
chmod 755 install-java8.bash
./install-java8.bash

# Get code from Github
git clone https://github.com/farzadrastegar/blog-microservices.git
cd blog-microservices
git checkout B3F

# Build code
./build-all.sh
```
##### Node 1 (auth server, service discovery server, edge server)
Reminder: before running the code snippet below, replace `YOUR_EUREKA_IP_HERE` with your Node 1 IP address (see [this](../part-1/img/labs_play-with-docker_com.png) picture).
```shell
# from inside the blog-microservices directory, go to microservices
cd ./microservices

# Run the auth server
cd support/auth-server; (nohup ./gradlew bootRun >out 2>&1 &); cd -

# Run Eureka (the service discovery server)
cd support/discovery-server; (nohup ./gradlew bootRun >out 2>&1 &); cd -

# Run the edge server from inside the blog-microservices directory
cd support/edge-server; (nohup ./gradlew bootRun >out 2>&1 &); cd -
```

##### Node 2 (monitoring service, Turbine service, composite service)
Reminder: before running the code snippet below, replace `YOUR_EUREKA_IP_HERE` with your Node 1 IP address (see [this](../part-1/img/labs_play-with-docker_com.png) picture).
```shell
# Exit from the Ubuntu container
exit

# Run a RabbitMQ container
docker run -it -d --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management

# Go back to the Ubuntu container
docker exec -it ubuntu16 /bin/bash
cd blog-microservices 
```

```shell
# from inside the blog-microservices directory, go to microservices
cd ./microservices

# Run the monitoring service
cd support/monitor-dashboard; (sed -i -e 's/192.168.0.08/YOUR_EUREKA_IP_HERE/' ./src/main/resources/application.yml); (nohup ./gradlew bootRun >out 2>&1 &); cd -

# Run the Turbine service
cd support/turbine; (sed -i -e 's/192.168.0.08/YOUR_EUREKA_IP_HERE/' ./src/main/resources/application.yml); (nohup ./gradlew bootRun >out 2>&1 &); cd -

# Run the product-composite service
cd composite/product-composite-service; (sed -i -e 's/192.168.0.08/YOUR_EUREKA_IP_HERE/' ./src/main/resources/application.yml); (nohup ./gradlew bootRun >out 2>&1 &); cd -

```

##### Node 3 (the core services)
Reminder: before running the code snippet below, replace `YOUR_EUREKA_IP_HERE` (find three spots) with your Node 1 IP address (see [this](../part-1/img/labs_play-with-docker_com.png) picture).
```shell
# from inside the blog-microservices directory, go to microservices
cd ./microservices

# Run the product service
cd core/product-service; (sed -i -e 's/192.168.0.08/YOUR_EUREKA_IP_HERE/' ./src/main/resources/application.yml)
; (nohup ./gradlew bootRun >out 2>&1 &); cd -

# Run the recommendation service
cd core/recommendation-service; (sed -i -e 's/192.168.0.08/YOUR_EUREKA_IP_HERE/' ./src/main/resources/application.yml)
; (nohup ./gradlew bootRun >out 2>&1 &); cd -

# Run the review service
cd core/review-service; (sed -i -e 's/192.168.0.08/YOUR_EUREKA_IP_HERE/' ./src/main/resources/application.yml)
; (nohup ./gradlew bootRun >out 2>&1 &); cd -
```

##### Node 4 (the product api service)
Reminder: before running the code snippet below, replace `YOUR_EUREKA_IP_HERE` with your Node 1 IP address (see [this](../part-1/img/labs_play-with-docker_com.png) picture).
```shell
# from inside the blog-microservices directory, go to microservices
cd ./microservices

# Run the product api service
cd api/product-api-service; (sed -i -e 's/192.168.0.08/YOUR_EUREKA_IP_HERE/' ./src/main/resources/application.yml); (nohup ./gradlew bootRun >out 2>&1 &); cd -
```


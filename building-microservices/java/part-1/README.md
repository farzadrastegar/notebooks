## What is this all about?

This is Part 1 of a series about building microservices with Spring Cloud and Netflix OSS. See [this link](https://callistaenterprise.se/blogg/teknik/2015/04/10/building-microservices-with-spring-cloud-and-netflix-oss-part-1/) for further detail.

## How to run?

In order to run code related to Part 1, there are three options.
* Follow [the instructions](https://callistaenterprise.se/blogg/teknik/2015/04/10/building-microservices-with-spring-cloud-and-netflix-oss-part-1/)  and run code in your favorite environment.
* Run the cells in the provided **Jupyter** notebook (tested on [Google Colab](http://colab.research.google.com)).
* Use **Docker** (tested on [Play with Docker](https://labs.play-with-docker.com/)).

### Jupyter on Google Colab

Load the provided Jupyter [notebook](https://github.com/farzadrastegar/notebooks/blob/master/building-microservices/java/building_microservices_part1.ipynb) on Google Colab. In case you are interested in the Eureka dashboard, make sure you are already logged into ngrok.io so you can use the URL that the notebook generates for the dashboard.



### Docker

The code snippets below run the Part 1 Github code in multiple Docker nodes through Ubuntu containers. These code snippets were tested on the [Play with Docker](https://labs.play-with-docker.com/) environment.

Note: Running code on Docker Swarm is NOT covered in Part 1.

##### For every node, run this first
```shell
# Run an Ubuntu container
docker run -it --rm --net=host ubuntu:16.04
apt update
apt install wget git -y

# Install Java 8
mkdir  -p /opt/jdk
cd /opt/jdk/
wget https://www.dropbox.com/s/raw/u21631du7ym6acj/jdk-8u102-linux-x64.tar.gz
tar -zxf jdk-8u102-linux-x64.tar.gz
update-alternatives --install /usr/bin/java java /opt/jdk/jdk1.8.0_102/bin/java 2000
cat << EOF >> /etc/environment
JAVA_HOME=/opt/jdk/jdk1.8.0_102
JRE_HOME=/opt/jdk/jdk1.8.0_102/jre
EOF
source /etc/environment
apt update
java -version
cd

# Get code from Github
git clone https://github.com/farzadrastegar/blog-microservices.git
cd blog-microservices
git checkout B1F

# Build code
./build-all.sh
```
##### Node 1 (service discovery server)
```shell
# Run Eureka (the service discovery server) from inside the blog-microservices directory
cd ./microservices/support/discovery-server
./gradlew bootRun
```

##### Node 2 (edge server)
Reminder: before running the code snippet below, replace `YOUR_EUREKA_IP_HERE` with your Node 1 IP address (see [this](./img/labs_play-with-docker_com.png) picture).
```shell
# Run the edge server from inside the blog-microservices directory
cd ./microservices/support/edge-server
sed -i -e 's/192.168.0.08/YOUR_EUREKA_IP_HERE/' ./src/main/resources/application.yml
./gradlew bootRun
```

##### Node 3 (the core services)
Reminder: before running the code snippet below, replace `YOUR_EUREKA_IP_HERE` (find three spots) with your Node 1 IP address (see [this](./img/labs_play-with-docker_com.png) picture).
```shell
# Run the core services from inside the blog-microservices directory
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

##### Node 4 (the composite service)
Reminder: before running the code snippet below, replace `YOUR_EUREKA_IP_HERE` with your Node 1 IP address (see [this](./img/labs_play-with-docker_com.png) picture).
```shell
# Run the composite service from inside the blog-microservices directory
cd ./microservices/composite/product-composite-service
sed -i -e 's/192.168.0.08/YOUR_EUREKA_IP_HERE/' ./src/main/resources/application.yml
./gradlew bootRun
```


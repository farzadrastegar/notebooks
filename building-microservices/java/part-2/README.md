The commands in the notebooks were tested with three nodes on labs.play-with-docker.com using Ubuntu 16.04.

```shell
docker run -it -d --name ubuntu16 --net=host ubuntu:16.04
docker exec -it ubuntu16 /bin/bash
```

Make sure to run rabbitmq on node 2 before running its corresponding notebook.

#### Rabbitmq in Docker
`docker run -it -d --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management`

#### How to run a Jupyter notebook using Docker

```shell
docker run -d --name notebook -p "8888:8888" --net=host -e GRANT_SUDO=yes --user root -v "${PWD}:/home/jovyan/work" jupyter/minimal-notebook

# Copy the token given through the command below
# Open port 8888, and paste the token as your credential
docker logs notebook 2>&1 | grep "token=" | head -1
```

The commands in the notebooks were tested with three nodes on labs.play-with-docker.com using Ubuntu 16.04.

`docker run -it -d --name ubuntu16 --net=host ubuntu:16.04
docker exec -it ubuntu16 /bin/bash`

Make sure to run rabbitmq on node 2 before running its corresponding notebook.

#### Rabbitmq in Docker
`docker run -it -d --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management`


Make sure to run rabbitmq on node 2 before running its corresponding notebook.

#### Rabbitmq in Docker
`docker run -it -d --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management`


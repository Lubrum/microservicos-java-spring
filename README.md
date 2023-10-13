Projeto de microserviços com Java, Spring, Rabbitmq, Eureka, Gateway, OpenFeign, Docker, MySQL e Flyway.

Comandos abaixo para criar containers do rabbitmq:

docker network create alura
docker run -d --rm --net alura --hostname rabbit1 --name rabbit1 -p 8085:15672 -e RABBITMQ_ERLANG_COOKIE=alura_secret rabbitmq:3.10-management
docker run -d --rm --net alura --hostname rabbit2 --name rabbit2 -p 8086:15672 -e RABBITMQ_ERLANG_COOKIE=alura_secret rabbitmq:3.10-management
docker run -d --rm --net alura --hostname rabbit3 --name rabbit3 -p 8087:15672 -e RABBITMQ_ERLANG_COOKIE=alura_secret rabbitmq:3.10-management

Comandos abaixo para criar um cluster com três nós de rabbitmq:

docker exec -it rabbit2 rabbitmqctl stop_app
docker exec -it rabbit2 rabbitmqctl reset
docker exec -it rabbit2 rabbitmqctl join_cluster rabbit@rabbit1
docker exec -it rabbit2 rabbitmqctl start_app

docker exec -it rabbit3 rabbitmqctl stop_app
docker exec -it rabbit3 rabbitmqctl reset
docker exec -it rabbit3 rabbitmqctl join_cluster rabbit@rabbit1
docker exec -it rabbit3 rabbitmqctl start_app

Para que outros nós assumam mensagens de nós que fiquem indisponíveis:

Rabbitmq admin -> policies -> add / update a policy:

Name: ha
Pattern: .*
Apply to: Exchanges and Queues
Definition: ha-mode = All


# Projeto de microserviços com Java, Spring, Rabbitmq, Eureka, Gateway, OpenFeign, Docker, MySQL e Flyway.

## Passos para criação de um cluster com três nós do RabbitMQ:

1. Criar containers do rabbitmq:

```sh
docker network create alura
docker run -d --rm --net alura --hostname rabbit1 --name rabbit1 -p 8085:15672 -e RABBITMQ_ERLANG_COOKIE=alura_secret rabbitmq:3.10-management
docker run -d --rm --net alura --hostname rabbit2 --name rabbit2 -p 8086:15673 -e RABBITMQ_ERLANG_COOKIE=alura_secret rabbitmq:3.10-management
docker run -d --rm --net alura --hostname rabbit3 --name rabbit3 -p 8087:15674 -e RABBITMQ_ERLANG_COOKIE=alura_secret rabbitmq:3.10-management
```

2. Criar um cluster com três nós, unindo os containers 'rabbit2' e 'rabbit3' do RabbitMQ ao container 'rabbit1':

```sh
docker exec -it rabbit2 rabbitmqctl stop_app
docker exec -it rabbit2 rabbitmqctl reset
docker exec -it rabbit2 rabbitmqctl join_cluster rabbit@rabbit1
docker exec -it rabbit2 rabbitmqctl start_app

docker exec -it rabbit3 rabbitmqctl stop_app
docker exec -it rabbit3 rabbitmqctl reset
docker exec -it rabbit3 rabbitmqctl join_cluster rabbit@rabbit1
docker exec -it rabbit3 rabbitmqctl start_app
```

3. Para que os nós assumam mensagens de que ficarem indisponíveis:

Abrir o RabbitMQ Admin no browser no localhost:15672 (endereço do 'rabbit1').

RabbitMQ Admin -> Policies -> Add / Update a policy:

> Name: ha
> Pattern: .*
> Apply to: Exchanges and Queues
> Definition: ha-mode = All


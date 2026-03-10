# Exercitii laboratorul 2 - Docker Swarm

Contine sursele pentru o stiva de servicii de biblioteca ce ajuta la gestiunea cartilor, cu trei servicii custom:

- serviciul ApiGateway mediaza accesul cu lumea exterioara si forwardeaza cererile HTTP catre serviciul de carti
- serviciul Books se ocupa de partea de business logic ce tine de carti si apeleaza serviciul IO pentru date
- serviciul IO gestioneaza datele sistemului si comunica cu baza de date.

## Exercitiul 1

Rulati:
```bash
docker compose -f docker-compose-local-build.yml up
```

Pentru cereri:
```bash
# GET 
curl --location 'http://localhost:3000/api/books'

# POST
curl --location 'http://localhost:3000/api/books' \
--header 'Content-Type: application/json' \
--data '{
    "title": "Morometii",
    "author": "Marin Preda", 
    "genre": "Realism"
}'
```

Opriti rularea:
```bash
docker compose -f docker-compose-local-build.yml down
```

## Exercitiul 2
```bash
docker images

docker tag docker-swarm-exercises-io-service florinrm/io-service
docker push florinrm/io-service

docker tag docker-swarm-exercises-gateway florinrm/gateway
docker push florinrm/gateway

docker tag docker-swarm-exercises-books-service florinrm/books-service
docker push florinrm/books-service
```

Apoi va uitati pe contul vostru de Docker Hub (comenzile de mai sus le faceti cu contul vostru).

## Exercitiul 3

Rulati:
```bash
docker compose -f docker-compose-local.yml up
```

Pentru cereri:
```bash
# GET 
curl --location 'http://localhost:3000/api/books'

# POST
curl --location 'http://localhost:3000/api/books' \
--header 'Content-Type: application/json' \
--data '{
    "title": "Morometii",
    "author": "Marin Preda", 
    "genre": "Realism"
}'
```

Opriti rularea:
```bash
docker compose -f docker-compose-local.yml down
```

## Docker in Docker

### Simple example

- creating container
```bash
docker run -d \
  --name dind \
  --privileged \
  -e DOCKER_TLS_CERTDIR= \
  docker:27-dind

docker ps
```

- running a command inside the container to create a new container inside the original one
```bash
docker exec -it dind sh

docker version
docker ps
docker run --rm hello-world
```

## Exercitiul 4

Folositi Docker in Docker - creati 3 containere: unul manager si 2 worker.

Creati reteaua pentru containere:
```bash
docker network create dind-net
```

Create containerele (primul e manager, celelalte doua worker):
```bash
docker run -d \
  --name swarm-manager \
  --hostname swarm-manager \
  --network dind-net \
  --privileged \
  -p 3000:3000 \
  -p 8080:8080 \
  -e DOCKER_TLS_CERTDIR= \
  docker:27-dind

docker run -d \
  --name swarm-worker1 \
  --hostname swarm-worker1 \
  --network dind-net \
  --privileged \
  -p 3001:3000 \
  -p 8081:8080 \
  -e DOCKER_TLS_CERTDIR= \
  docker:27-dind

docker run -d \
  --name swarm-worker2 \
  --hostname swarm-worker2 \
  --network dind-net \
  --privileged \
  -p 3002:3000 \
  -p 8082:8080 \
  -e DOCKER_TLS_CERTDIR= \
  docker:27-dind
```

Initiem swarm-ul:
```bash
docker exec swarm-manager docker swarm init --advertise-addr eth0

docker exec swarm-manager docker swarm join-token worker -q

TOKEN=$(docker exec swarm-manager docker swarm join-token worker -q)
```

Adaugam workerii:
```bash
docker exec swarm-worker1 docker swarm join \
  --token "$TOKEN" \
  --advertise-addr eth0 \
  swarm-manager:2377

docker exec swarm-worker2 docker swarm join \
  --token "$TOKEN" \
  --advertise-addr eth0 \
  swarm-manager:2377

docker exec swarm-manager docker node ls
```

Copiem fisierele si folderul de baze de date in containerul manager:
```bash
docker exec swarm-manager mkdir -p /stack/Database

docker cp ./docker-compose-stack.yml swarm-manager:/stack/stack.yaml
docker cp ./Database/init-db.sql swarm-manager:/stack/Database/init-db.sql

docker exec swarm-manager ls -R /stack
```

Facem deploy la stack in containerul manager:
```bash
docker exec swarm-manager sh -c 'cd /stack && docker stack deploy -c stack.yaml laborator3'

docker exec swarm-manager docker stack ls
docker exec swarm-manager docker stack services laborator3
docker exec swarm-manager docker stack ps laborator3
```

Stergem tot:
```bash
docker exec swarm-manager docker stack rm laborator3
docker rm -f swarm-manager swarm-worker1 swarm-worker2
docker network rm dind-net
```
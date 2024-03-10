# Exercitii Docker Swarm

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

## Exercitiul 4

TODO

## Exercitiul 5

TODO
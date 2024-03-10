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

Deschideti Play with Docker in browser si creati doua masini virtuale. Una va avea rol de manager.

Va conectati de pe local la masinile virtuale create in Play with Docker:
```bash
ssh ip172-18-0-35-cnn1o9aim2rg00cneoo0@direct.labs.play-with-docker.com # prima masina virtuala

ssh ip172-18-0-9-cnn1o9aim2rg00cneoo0@direct.labs.play-with-docker.com # a doua masina virtuala 
```

Pe prima masina virtuala initializati cluster-ul de Docker Swarm:
```bash
docker swarm init --advertise-addr 192.168.0.8 # adresa IP luata din pagina de UI sau din ip addr
```

Apoi copiati fisierul de Docker Stack + fisierul de SQL in prima masina virtuala:
```bash
scp -r docker-compose-stack.yaml Database/ ip172-18-0-35-cnn1o9aim2rg00cneoo0@direct.labs.play-with-docker.com:~/
```

Pe prima masina virtuala rulati comanda de mai sus pentru a porni serviciul Docker:
```bash
docker stack deploy -c docker-compose-stack.yml lab2
```

Verificam ca ruleaza:
```bash
docker stack ps lab2
```

Adaugam a doua masina virtuala in cluster-ul Docker (de rulat in masina virtuala):
```bash
docker swarm join --token SWMTKN-1-3n0wrxbcvrh3do7oflvhghvx2xhagc4dcrcarvwef1mv7hqo4k-f42t08hq62365kdd96vqxyc06 192.168.0.8:2377
```

De rulat (pentru ambele masini):
```bash
curl --location --request GET 'http://ip172-18-0-35-cnn1o9aim2rg00cneoo0-3000.direct.labs.play-with-docker.com/api/books/' \
--header 'Content-Type: application/json'

curl --location 'http://ip172-18-0-35-cnn1o9aim2rg00cneoo0-3000.direct.labs.play-with-docker.com/api/books/' \
--header 'Content-Type: application/json' \
--data '{
    "title": "Morometii",
    "author": "Marin Preda", 
    "genre": "Realism"
}'

curl --location 'http://ip172-18-0-9-cnn1o9aim2rg00cneoo0-3000.direct.labs.play-with-docker.com/api/books/' \
--header 'Content-Type: application/json'
```

## Exercitiul 5

Deschideti Play with Docker in browser si creati doua masini virtuale. Una va avea rol de manager.

Va conectati de pe local la masinile virtuale create in Play with Docker:
```bash
ssh ip172-18-0-4-cnn2d8ol2o9000fbhl8g@direct.labs.play-with-docker.com # prima masina virtuala

ssh ip172-18-0-14-cnn2d8ol2o9000fbhl8g@direct.labs.play-with-docker.com # a doua masina virtuala 
```

Pe prima masina virtuala initializati cluster-ul de Docker Swarm:
```bash
docker swarm init --advertise-addr 192.168.0.28 # adresa IP luata din pagina de UI sau din ip addr
```

Pe prima masina virtuala cream secretele:
```bash
docker secret create lab3-user-secret Secrets/lab3-user-secret.txt

docker secret create lab3-password-secret Secrets/lab3-password-secret.txt

docker secret ls # afisam secretele create mai sus
```

Pe prima masina virtuala rulati comanda de mai sus pentru a porni serviciul Docker:
```bash
docker stack deploy -c docker-compose-stack-secrets.yml lab2
```

Verificam ca ruleaza:
```bash
docker stack ps lab2
```

Adaugam a doua masina virtuala in cluster-ul Docker (de rulat in masina virtuala):
```bash
docker swarm join --token SWMTKN-1-3n0wrxbcvrh3do7oflvhghvx2xhagc4dcrcarvwef1mv7hqo4k-f42t08hq62365kdd96vqxyc06 192.168.0.8:2377
```

De rulat (pentru ambele masini):
```bash
curl --location --request GET 'http://ip172-18-0-4-cnn2d8ol2o9000fbhl8g-3000.direct.labs.play-with-docker.com/api/books/' \
--header 'Content-Type: application/json' 

curl --location 'http://ip172-18-0-4-cnn2d8ol2o9000fbhl8g-3000.direct.labs.play-with-docker.com/api/books/' \
--header 'Content-Type: application/json' \
--data '{
    "title": "Delirul",
    "author": "Marin Preda", 
    "genre": "Realism"
}'

curl --location 'http://ip172-18-0-14-cnn2d8ol2o9000fbhl8g-3000.direct.labs.play-with-docker.com/api/books/' \
--data ''
```
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

# Demo laborator 3 - Docker Swarm persistence + Kong

## Docker Swarm persistence

Cream trei masini virtuale folosind Docker Machine (mai precis, un fork facut de Rancher - vezi [aici](https://gcbw.github.io/docker.github.io/machine/install-machine/)) - o sa facem doar pentru NFS:
```bash
docker-machine create --driver virtualbox myvm1

docker-machine create --driver virtualbox myvm2

docker-machine create --driver virtualbox myvm3

docker-machine env myvm1 # aflam adresa IP a masinii virtuale

docker-machine env myvm2

docker-machine env myvm3

docker-machine ssh myvm1

docker-machine ssh myvm2

docker-machine ssh myvm3

docker-machine scp docker-stack-nfs.yml myvm1:. # copiem fisierul de Docker Stack pe myvm1
```

Initializam Swarm-ul pe myvm1:
```bash
docker swarm init --advertise-addr 192.168.99.100 # adresa luata din ip addr
```

Adaugam myvm2 si myvm3 in Swarm:
```bash
docker swarm join --token SWMTKN-1-4ca0w5i9vyma6s6of8zdileap7lk8tdm80qtyao03seioebthf-0uqbrlgrlo62ed4145behvqhm 192.168.99.100:2377
```

Promovam nodul corespunzator myvm2 ca nod manager din nodul lider (myvm1):
```bash
docker node ls # luam id-ul nodului

docker node promote c8djejjv8nrh3xyycbt27xf7d
```

Lansam stiva de servicii (din myvm1):
```bash
docker stack deploy -c docker-stack-nfs.yml lab3-nfs
```

## Kong
Cream 3 masini virtuale in Play With Docker (sau in Docker Machine, daca nu avem deja).

Copiem fisierul de Docker Stack care are Kong pe masina lider:
```bash
scp docker-stack-kong.yml  ip172-18-0-53-cnvvqpaim2rg00cceblg@direct.labs.play-with-docker.com:.

scp -r Database/ ip172-18-0-53-cnvvqpaim2rg00cceblg@direct.labs.play-with-docker.com:.
```

Apoi copiem folder-ul de Kong pe masina lider (acesta este folosit ca volum in fisierul de Docker Stack):
```bash
scp -r Kong/ ip172-18-0-53-cnvvqpaim2rg00cceblg@direct.labs.play-with-docker.com:.
```

Dam request:
```bash
curl --location 'http://ip172-18-0-53-cnvvqpaim2rg00cceblg.direct.labs.play-with-docker.com/api/books/' \
--header 'Content-Type: application/json' \
--header 'apikey: mobylab' \
--data '{
    "title": "Delirul",
    "author": "Marin Preda", 
    "genre": "Realism"
}'
```

Pentru Kong Plugins:
```bash
scp docker-stack-kong-plugins.yml   ip172-18-0-53-cnvvqpaim2rg00cceblg@direct.labs.play-with-docker.com:.

scp -r prometheus/  ip172-18-0-53-cnvvqpaim2rg00cceblg@direct.labs.play-with-docker.com:.
```

Pornim stiva de servicii pe masina lider:
```bash
docker stack deploy -c docker-stack-kong-plugins.yml lab3-kong-plugins
```

Apoi accesam in browser: http://ip172-18-0-53-cnvvqpaim2rg00cceblg-3000.direct.labs.play-with-docker.com/ pentru Grafana

# Laborator 4 - Portainer + CI/CD
## Portainer

```bash
gitlab-runner register  --url https://gitlab.com  --token glrt-fVzZhmWkfn6EVXwWYLPW
```
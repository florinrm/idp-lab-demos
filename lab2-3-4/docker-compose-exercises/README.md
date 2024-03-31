# Exercitii Docker Compose

Aici vom folosi Docker Compose V2.

## Exercitiul 1

Vedeti in fisierul `docker-compose-first.yaml` configuratiile cerute.

Rulati:
```bash
docker compose -f docker-compose-first.yaml up # 
```

Dam comenzile:
```bash
# POST
curl --location 'http://localhost:5555/api/books' \
--header 'Content-Type: application/json' \
--data '{
    "title":"Morometii",
    "author":"Marin Preda"
}'

# GET
curl --location --request GET 'http://localhost:5555/api/books' \
--header 'Content-Type: application/json'
```

Pentru a vedea starile containerelor:
```bash
docker compose -f docker-compose-first.yaml ps
```

Pentru a opri containerele:
```bash
docker compose -f docker-compose-first.yaml down # 
```

## Exercitiul 2

Vedeti in fisierul `docker-compose-second.yaml` configuratiile cerute, care includ Adminer.

Rulati:
```bash
docker compose -f docker-compose-second.yaml up # 
```

Dam comenzile:
```bash
# POST
curl --location 'http://localhost:5555/api/books' \
--header 'Content-Type: application/json' \
--data '{
    "title":"Morometii",
    "author":"Marin Preda"
}'

# GET
curl --location --request GET 'http://localhost:5555/api/books' \
--header 'Content-Type: application/json'
```

Pentru a vedea starile containerelor:
```bash
docker compose -f docker-compose-second.yaml ps
```

Pentru a accesa Adminer: http://[::]:8080 - selectati ca tip de baza de date PostgreSQL si folositi datele de configurare din fisierul de Docker Compose pentru logging.

Pentru a opri containerele:
```bash
docker compose -f docker-compose-second.yaml down # 
```

## Exercitiul 3

Rulati:
```bash
docker compose -f docker-compose-third-base.yaml -f docker-compose-third-secrets.yaml up
```

Pentru a vedea starile containerelor:
```bash
docker compose -f docker-compose-third-base.yaml ps
```

Pentru a accesa Adminer: http://[::]:8080 - selectati ca tip de baza de date PostgreSQL si folositi datele de configurare din fisierul de Docker Compose si secrete pentru logging.

Pentru a opri containerele:
```bash
docker compose -f docker-compose-third-base.yaml down
```
# Laboratorul 5 - Monitorizare, vizualizare, logging, cozi de mesaje

## Monitorizare Docker

### Prin CLI
Putem monitoriza resursele containerelor Docker:
```bash
docker run --name myalpine -d -it alpine

docker stats myalpine

docker run --name myalpine2 -d -it alpine

docker stats

docker stats --format "{{.Container}}: {{.CPUPerc}}" # pentru a vedea cat CPU consuma fiecare container

docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}" # pentru a vedea mai frumos statisticile
```

### Prin Docker Remote API
```bash
docker container ls

curl --unix-socket /var/run/docker.sock http://localhost/containers/ae486f668303/stats
```

### Monitorizare evenimnente Docker
Putem monitoriza evenimentele Docker pe masina host:
```bash
docker system events # intr-un terminal

docker run --name myalpine2 -d -it alpine # in alt terminal
```

## Monitorizare aplicatie + vizualizare + logging + cozi de mesaje


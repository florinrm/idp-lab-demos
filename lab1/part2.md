- cream fisierul Dockerfile:
```Dockerfile
FROM node:14.13.0-stretch

COPY package.json ./

RUN npm install

COPY server.js /usr/src/app/ # fiti atenti sa puneti la finalul numelui unui folder slash, altfel se va interpreta ca un fisier!

EXPOSE 8080

CMD ["node", "/usr/src/app/server.js"]
```
- cream imaginea pe baza Dockerfile-ului:
```bash
docker build -t nodejstest .
```
- vedem ca s-a creat imaginea cu numele nodejstest:
```bash
docker images | grep "nodejstest"
```
- pornim un container de nodejstest, in background, cu maparea de local 12345 la cea din container (8080):
```bash
docker container run -p 12345:8080 -d nodejstest
```
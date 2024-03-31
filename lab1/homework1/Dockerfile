FROM node:21-alpine3.18

COPY package.json ./

RUN npm install

COPY server.js /usr/src/app/

EXPOSE 8080

CMD ["node", "/usr/src/app/server.js"]
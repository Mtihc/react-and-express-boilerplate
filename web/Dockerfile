FROM node:lts-slim

RUN mkdir -p /usr/src/app

WORKDIR /usr/src/app

COPY ./client ./client
COPY ./api ./api

RUN npm run-script build --prefix client

ENV PORT=80

CMD [ "npm", "run-script", "start", "--prefix", "api" ]

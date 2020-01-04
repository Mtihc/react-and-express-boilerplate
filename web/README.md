# About
This is a boilerplate to build a full stack web application using a React frontend and Express backend.

- Used `create-react-app` and `express-generator` to generate code to easily get started.
- Created `Dockerfile` and `docker-compose.yml` for production
- Made some changes to enable hot reloading while developing

# Steps to reproduce

1. [Set up your environment](#1-set-up-your-environment)
2. [Set up the app](#2-set-up-the-app)
3. [Set up Dockerfile and docker-compose](#3-set-up-dockerfile-and-docker-compose)
4. [Connect the React app and the Express backend](#4-connect-the-react-app-and-the-express-backend)
5. [Test the app](#5-test-the-app)
6. [Set up local development](#6-set-up-local-development)

### 1. Set up your environment


1. Install `nvm`
   [Read this](https://github.com/nvm-sh/nvm), or Google it.

2. Install `node`
   ```console
   nvm install node
   ```
   
3. Install `docker`
   [Read this](https://docs.docker.com/install/), or Google it.

4. Install `docker-compose`
   [Read this](https://docs.docker.com/compose/install/), or Google it.


### 2. Set up the app

1. Make a directory for your app
   ```console
   cd ~
   mkdir -p app/web
   ```
2. Use `create-react-app` to make the frontend. [Read this](https://github.com/facebook/create-react-app) for more info.
   ```console
   cd ~/app/web
   npx create-react-app client
   cd ~/app/web/client
   ```
3. Use `express-generator` to make the backend API. [Read this](https://expressjs.com/en/starter/generator.html) for more info.
   ```console
   cd ~/app
   npx express-generator api
   cd api
   npm install
   ```
4. Copy `.gitignore`
   > The `create-react-app` command creates a nice `.gitignore` file.
   > But the `express-generator` does not.

   You might want to copy the `.gitignore` from the `client` folder to the `api` folder.

   ```console
   cp client/.gitignore api/.gitignore
   ```

### 3. Set up Dockerfile and docker-compose

1. Create a `Dockerfile` in the main `app` folder
   ```console
   cd ~/app
   ```
   ```dockerfile
   FROM node:lts-slim
   
   RUN mkdir -p /usr/src/app
   
   WORKDIR /usr/src/app

   COPY ./client ./client
   COPY ./api ./api

   RUN npm run-script build --prefix client

   ENV PORT=80

   CMD [ "npm", "run-script", "start", "--prefix", "api" ]

   ```

2. Create a `docker-compose.yml` in the main `app` folder
   ```console
   cd ~/app
   ```
   ```yaml
   version: '3'
   
   services:
     web:
       container_name: app_web
       build:
         context: ./web
         dockerfile: Dockerfile
       image: mitchstoffels/web
       ports:
         - "80:80"
   ```

### 4. Connect the React app and the Express backend

- Remove the `indexRouter` from `~/app/web/api/app.js`.
  ```diff
  -var indexRouter = require('./routes/index');
  -app.use('/', indexRouter);
  ```
- Delete the router
  ```console
  rm ~/app/web/api/routes/index.js
  ```
- Delete the related view
  ```console
  rm ~/app/web/api/views/index.jade
  ```
- In `~/app/web/api/app.js` make it so the files in `~/app/web/client/build` are accessible
  ```diff
  +app.use(express.static(path.join(__dirname, '..', 'client', 'build')));
  ```
- In `~/app/web/api/app.js` make it so the home page is the React app.
  ```diff
  +app.use(express.static(path.join(__dirname, '..', 'client', 'build')));
  +app.get('/', function(req, res, next) {
  +  res.sendFile(path.join(__dirname, '..', 'client', 'build', 'index.html'))
  +});
  ```


### 5. Test the app

The app is finally set up. Run `docker-compose up` to start the app.

```console
docker-compose up
```

Browse to wherever your app is hosted (like http://localhost) and you should see the React logo.


### 6. Set up local development

1. Edit `~/app/web/api/bin/www` to change the default port
   ```diff
   -var port = normalizePort(process.env.PORT || '3000');
   +var port = normalizePort(process.env.PORT || '3001');
   ```
2. Edit `~/app/web/client/package.json` to add `"proxy"`
   ```diff
      "name": "client",
      "version": "0.1.0",
      "private": true,
   +  "proxy": "http://localhost:3001",
      "dependencies": {
   ```
3. Install `nodemon` in the backend project, for hot reloading
   ```console
   cd ~/app/web/api
   npm install nodemon --save-dev
   ```
4. Edit `~app/web/api/package.json` to start backend with `nodemon` instead of `node`
   ```diff
      "scripts": {
        "start": "node ./bin/www"
   +    "dev": "nodemon ./bin/www"
      },
   ```
5. Run both frontend and backend separately
   - In one session, run:
     ```console
     npm run-script start --prefix client
     ```
   - In another session, run:
     ```console
     npm run-script dev --prefix api
     ```
   - Hooray! While developing, changes will be applied without having to refresh the page


# Where to go from here?
1. Change the description and title in `~/app/web/client/public/index.html`
2. Replace the logos and favicon in `~/app/web/client/public/`
3. Change the name and short_name in `~/app/web/client/public/manifest.json`
4. Edit `~/app/web/client/src/App.js` and delete `~/app/web/client/src/logo.svg`

Also, maybe...
1. Add a CSS framework, like bootstrap
2. Add a database
3. Add user register/login features

# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).
hi

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in your browser.

The page will reload when you make changes.\
You may also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can't go back!**

If you aren't satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you're on your own.

You don't have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn't feel obligated to use this feature. However we understand that this tool wouldn't be useful if you couldn't customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Code Splitting

This section has moved here: [https://facebook.github.io/create-react-app/docs/code-splitting](https://facebook.github.io/create-react-app/docs/code-splitting)

### Analyzing the Bundle Size

This section has moved here: [https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size](https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size)

### Making a Progressive Web App

This section has moved here: [https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app)

### Advanced Configuration

This section has moved here: [https://facebook.github.io/create-react-app/docs/advanced-configuration](https://facebook.github.io/create-react-app/docs/advanced-configuration)

### Deployment

This section has moved here: [https://facebook.github.io/create-react-app/docs/deployment](https://facebook.github.io/create-react-app/docs/deployment)

### `npm run build` fails to minify

This section has moved here: [https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify](https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify)

## Paso a Paso

### Estructura del proyecto (repositorio)

```
├── Dockerfile
├── README.md
├── imag/
├── node_modules/
├── package-lock.json
├── package.json
├── public/
├── src/
└── yarn.lock
```

### Creación del Dockerfile

Creamos un archivo que se llame ***Dockerfile***, en el cual contenerizaremos la aplicación. Este Dockerfile debe contener las siguientes instrucciones:

```
FROM node:20.5.1-alpine
RUN mkdir -p /app
WORKDIR /app
COPY . .
RUN npm cache clean --force
RUN npm install
RUN npm run build
EXPOSE 3000
ENTRYPOINT ["npm", "run", "start"]
```

### Creación de la automatización con GitHub Actions

Utilizaremos GitHub Actions para automatizar el proceso de contenerización y despliegue de la aplicación. A continuación se muestra el archivo de configuración YAML para GitHub Actions:
```
name: Docker Image CI

on:
  push:
    branches: [ "main" ]
 
jobs:

  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout Repo
      uses: actions/checkout@v3
    - name: Docker Setup Buildx
      uses: docker/setup-buildx-action@v3.0.0
    - name: Docker Login
      uses: docker/login-action@v1
      with:
            username: ${{ secrets.DOCKER_USERNAME }}
            password: ${{ secrets.DOCKER_PASSWORD}}
    - name: Build and Push Docker image
      uses: docker/build-push-action@v5.0.0
      with:
          context: .  # Ruta al contexto de construcción (puede ser el directorio actual)
          file: ./Dockerfile  # Ruta al Dockerfile
          push: true  # Habilitar el empuje de la imagen
          tags: ${{ secrets.DOCKER_USERNAME }}/node-ci:latest  # Nombre y etiqueta de la imagen
      env:
          DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
          DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
```

_Nota:_ 
Para la correcta ejecución del flujo, se debe tener una cuenta en DockerHub. Como podemos ver en la parte final del YAML, se intenta acceder al username y password de Docker para poder subir la imagen a DockerHub.
Además, es necesario configurar los Actions Secrets en GitHub de la siguiente manera:

![image](https://github.com/santiagoarevalo/ci-github-2024-1/assets/71450411/6f106c75-948f-4eee-b393-2c75cf4ab547)

Estos son los nombres de los dos secretos y dentro, como valor, tendría las respectivas credenciales de inicio de sesión en Docker.

 ## Ejecución del flujo

Teniendo correctamente configurado lo anterior, podemos ver ejecutado correctamente el flujo:

![image](https://github.com/santiagoarevalo/ci-github-2024-1/assets/71450411/d2d204b4-e046-474f-b4e2-2265616b7661)
![image](https://github.com/santiagoarevalo/ci-github-2024-1/assets/71450411/fdc2188f-dfdf-4fc2-8dcb-2cb4ac7c97a8)

Y, finalmente, vemos la imagen de la aplicación desplegada en DockerHub:

![image](https://github.com/santiagoarevalo/ci-github-2024-1/assets/71450411/45e3dfda-fa87-4368-9d2a-7a4f882b93a9)

- [Dockerhub Image](https://hub.docker.com/r/santiagoarevalov/node-ci)


By
[**Santiago Arévalo Valencia**](https://github.com/santiagoarevalo) 👨🏽‍💻

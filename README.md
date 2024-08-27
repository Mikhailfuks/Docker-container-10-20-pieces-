tasks 11

FROM tomcat:9.0-jre8

COPY target/*.war /usr/local/tomcat/webapps/

docker build -t java-app .
docker run -d -p 8080:8080 java-app

docker-compose up -d
tasks 12 
FROM golang:1.17

WORKDIR /app

COPY FILE 

RUN go build -o main

CMD ["/app/main"]



package main

import (
  "fmt"
  "net/http"
)

func main() {
  http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello from Go!")
  })

  fmt.Println("Server listening on port 8080")
  http.ListenAndServe(":8080", nil)
}


docker build -t go-app .
docker run -d -p 8080:8080 go-app

tasks 13 

FROM node:16-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY FILE 

RUN npm run build

FROM nginx:latest

COPY --from=build /app/dist/angular-app /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

docker build -t angular-app .
docker run -d -p 80:80 angular-app

tasks 14 


FROM openjdk:11-jre-slim

WORKDIR /app

COPY target/*.jar app.jar

CMD ["java", "-jar", "app.jar"]

docker build -t spring-boot-app .
docker run -d -p 8080:8080 spring-boot-app

tasks 15


FROM redis:latest

docker build -t redis-db .
docker run -d -p 6379:6379 --name redis-db redis-db


FROM mongo:latest

docker build -t mongo-db .
docker run -d -p 27017:27017 --name mongo-db mongo-db

tasks 16

version: '3.7'

services:
  nginx:
    build: ./nginx
    ports:
      - "80:80"
    depends_on:
      - app
    networks:
      - mynetwork

  app:
    build: ./node-app
    ports:
      - "3000:3000"
    depends_on:
      - db
    networks:
      - mynetwork

  db:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: mypassword
      MYSQL_DATABASE: mydatabase
      MYSQL_USER: myuser
    ports:
      - "3306:3306"
    networks:
      - mynetwork

networks:
  mynetwork:
    driver: bridge

docker-compose up -d

tasks 17

FROM node:16-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY FILE 

CMD ["npm", "start"]

trigger:
  - master

pool:
  vmImage: 'ubuntu-latest'

variables:
  DOCKER_HUB_USERNAME: 'your_docker_hub_username'
  DOCKER_HUB_PASSWORD: 'your_docker_hub_password'
  AZURE_SUBSCRIPTION_ID: 'your_azure_subscription_id'
  AZURE_RESOURCE_GROUP: 'your_azure_resource_group'
  AZURE_WEBAPP_NAME: 'your_azure_webapp_name'

steps:
- task: Docker@2
  inputs:
    command: 'build'
    containerImage: $(DOCKER_HUB_USERNAME)/node-app:latest
    dockerfile: 'Dockerfile'
- task: Docker@2
  inputs:
    command: 'push'
    containerImage: $(DOCKER_HUB_USERNAME)/node-app:latest
    registry: 'dockerhub'
    username: $(DOCKER_HUB_USERNAME)
    password: $(DOCKER_HUB_PASSWORD)
- task: AzureWebAppDeployment@2
  inputs:
    azureSubscription: $(AZURE_SUBSCRIPTION_ID)
    resourceGroupName: $(AZURE_RESOURCE_GROUP)
    appName: $(AZURE_WEBAPP_NAME)
    package: '$(System.DefaultWorkingDirectory)/app/build'

    tasks 18

    version: '3.7'

services:
  nginx:
    build: ./nginx
    ports:
      - "80:80"
    environment:
      MYSQL_HOST: mysql
      MYSQL_PORT: 3306
      MYSQL_USER: myuser
      MYSQL_PASSWORD: mypassword
      MYSQL_DATABASE: mydatabase
    depends_on:
      - mysql
    networks:
      - mynetwork

  mysql:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: mypassword
      MYSQL_DATABASE: mydatabase
      MYSQL_USER: myuser
    ports:
      - "3306:3306"
    networks:
      - mynetwork

networks:
  mynetwork:
    driver: bridge

    docker-compose up -d


    tasks 19 

FROM python:3.9-slim

ENV PYTHONUNBUFFERED=1

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY FILE 

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]


version: '3.7'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    depends_on:
      - db

  db:
    image: postgres:latest
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    ports:
      - "5432:5432"

docker-compose up -d

tasks 20 


FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY FILE 

CMD ["python", "app.py"]


from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello from Flask!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)


apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
      - name: flask-app
        image: your_docker_hub_username/flask-app:latest
        ports:
        - containerPort: 8080


apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
      - name: flask-app
        image: your_docker_hub_username/flask-app:latest
        ports:
        - containerPort: 8080

# Построить образ Docker
docker build -t your_docker_hub_username/flask-app:latest .

# Отправить образ в Docker Hub
docker push your_docker_hub_username/flask-app:latest

# Развернуть приложение в Kubernetes
kubectl apply -f deployment.yaml

Maxime RISS et Luca POLGAR

****TP1:****

****question 1.1****
creation du dockerfile:

```
FROM postgres:14.1-alpine

ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd
```

nous buildons notre docker : 

```
docker build -t softsouls/database .
```

et nous lancons docker run :
(nous lions la sortie du conteneur 5432 sur la sortie 5431 de notre réseau)

```
docker run -p 5431:5432--name database softsouls/database
```


****question 1.2****
cela nous permets de centraliser des étapes qui renécessiterait des opérations déjà fournis ailleurs.
Par exemple si 2 stages ont besoin d'un même artifact que l'on doit créer au préalable, on peut le copier depuis une autre étape. Cela nous fais gagner du temps 

Etape 1 du dockerfile - build:
Depuis un environnement maven,
Nous copions le pom.xml ainsi que les sources
pour apres lancer la creation d'un package et donc la génération d'un executable

Etape 2 du dockerfile - run:
Depuis un environnement Java 17
nous copions le jar généré par le mvn package
on execute avec le jar

****question 1.3****
docker compose up (lancement du build avec démarrage des conteneurs. -d permets de lancer en arrière plan)
docker compose logs
docker compose down (pour stopper les conteneurs et effacer les images)
docker compose stop (stopper les conteneurs)
docker compose build
docker compose restart


****question 1.4****

```
version: '3.3'
services:
  backend:
    container_name: backend
    build: ./simple-api   #indication du chemin vers dockerfile
    networks:
      - irc  #branché au réseau irc
    depends_on:
      - database   #attente du déploiement du conteneur database pour déployer celui-ci

  database:
    container_name: database
    restart: always
    build: ./database
    networks:
      - irc
    env_file:
      - database/.env

  httpd:
    container_name: reverse_proxy
    build: ./httpd
    ports:
      - "80:80"
    networks:
      - irc
    depends_on:
      - backend


volumes:
  my_db_volume:
    driver: local

networks:
  irc:
```



****question 1.5****

```
# Note: tags has to be all lower-case
tags:  ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-simple-api

tags:  ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-database

# Note: tags has to be all lower-case
tags:  ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-httpd

# build on feature branches, push only on main branch
push: ${{ github.ref == 'refs/heads/main' }}
```

docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
docker push NAME[:TAG]
		   
		   
		   
		   
		   



****TP2:****

****2.1****
permets de conteneurisé des process de tests unitaires/acceptance et intégration compatible avec plusieurs technos dont JUnit.
possibilité de deployer pluseurs testcontenaire en meme temps 




****2.2****

```
name: CI devops 2023
on:
  #On lance les CI à chaque push sur les branches main et develop
  push:
    branches: 
        - 'main'
        - 'develop'
  pull_request:

jobs:
  test-backend: 
    runs-on: ubuntu-22.04
    steps:
     #checkout your github code using actions/checkout@v2.5.0
      - uses: actions/checkout@v2.5.0

     #do the same with another action (actions/setup-java@v3) that enable to setup jdk 17
      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'adopt'

     #finally build your app with the latest command
     #lancement du verify avec sonar relié à notre projectKey et OrganisationKey
      - name: Build and test with Maven
        working-directory: ./tp2/simple-api   #permets de se placer dans ce dossier pour chaque commande. Utile car mvn ne se lance que dans le dossier ou un pom.xml est présent
        run: mvn -B verify sonar:sonar -Dsonar.projectKey=reyraluca_devops_TP2 -Dsonar.organization=tp-devops -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${{ secrets.SONAR_TOKEN }}

#second job : avec build et publication des images dockers
        
  build-and-push-docker-image:
     needs: test-backend
     # run only when code is compiling and tests are passing
     runs-on: ubuntu-22.04

     # steps to perform in job
     steps:
       - name: Checkout code
         uses: actions/checkout@v2.5.0
         
       - name: Login to DockerHub
         run: docker login -u ${{ secrets.DOCKERHUB_USERNAME }} -p ${{ secrets.DOCKERHUB_TOKEN }}

       - name: Build image and push backend
         uses: docker/build-push-action@v2
         with:
           # relative path to the place where source code with Dockerfile is located
           context: ./tp2/simple-api
           # Note: tags has to be all lower-case
           tags:  ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-simple-api
           # build on feature branches, push only on main branch
           push: ${{ github.ref == 'refs/heads/main' }}

       - name: Build image and push database
         uses: docker/build-push-action@v3
         with:
           # relative path to the place where source code with Dockerfile is located
           context: ./tp2/database
           # Note: tags has to be all lower-case
           tags:  ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-database
           # build on feature branches, push only on main branch
           push: ${{ github.ref == 'refs/heads/main' }}

       - name: Build image and push httpd
         uses: docker/build-push-action@v3
         with:
           # relative path to the place where source code with Dockerfile is located
           context: ./tp2/httpd
           # Note: tags has to be all lower-case
           tags:  ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-httpd
           # build on feature branches, push only on main branch
           push: ${{ github.ref == 'refs/heads/main' }}
```



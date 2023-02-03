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



question 1.5 // revoir

           # Note: tags has to be all lower-case
           tags:  ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-simple-api
           
           tags:  ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-database
           
           # Note: tags has to be all lower-case
           tags:  ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-httpd
		   
		   
		   
		   
		   
		   



TP2:

2.1


facile les test unitaire / acceptance / integration en complete isolation et étant compatible avec la cible
deployé pluseurs testcontenaire en meme temps 






2.2














2.3 main.yml 



3.1



3.2



3.3

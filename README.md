Maxime RISS et Luca POLGAR

TP1:

question 1.1
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


question 1.2
pour paralleliser nous fais gagner du temps et paralleliser la chage du build pour une meilleur exéccution

build:
nous nous positionnnons dans un environnement maven
puis nous copions le pom.xml vers les sources
pour apres lancer la creation d'un package

run:
nous nous positionnnons sur notre application 
nous copions le package généré par le build
nous exécution le package java


question 1.3
docker compose up 
docker compose logs
docker compose down
docker compose stop
docker compose build
docker compose restart


question 1.4

```
version: '3.3'
services:
  backend:
    container_name: backend
    build: ./simple-api
    networks:
      - irc
    depends_on:
      - database

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

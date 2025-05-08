# Udemy Course Microservices with Spring Boot, Docker, Kubernetes Section4
https://www.udemy.com/course/master-microservices-with-spring-docker-kubernetes/
## spring version: 3.4.4
## Java 21


## Documentation
After adding the library, the swagger page is accessible through address 
http://localhost:8080/swagger-ui/index.html,
http://localhost:8090/swagger-ui/index.html,
http://localhost:9000/swagger-ui/index.html.


## Dockerization accounts by Docker file

### prepare JAR
cd accounts
mvn clean install
generated target\accounts-0.0.1-SNAPSHOT.jar

mvn spring-boot:run
application accounts is running on port 8080

java -jar .\target\accounts-0.0.1-SNAPSHOT.jar
application accounts is running on port 8080


### build the image
PS C:\Training\Microservices\section4\accounts> docker build . -t jjasonek/accounts:s4

PS C:\Training\Microservices\section4\accounts> docker images
REPOSITORY                  TAG       IMAGE ID       CREATED              SIZE
jjasonek/accounts          s4        a25eb3d0b593   About a minute ago   503MB
...

PS C:\Training\Microservices\section4\accounts> docker inspect a25


### run the container
PS C:\Training\Microservices\section4\accounts> docker run -d -p 8080:8080 jjasonek/accounts:s4
1c6d250644c21b96da477cf1d56b8fa0015543cd33c935625fac11bb5a740402

PS C:\Training\Microservices\section4\accounts> docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED          STATUS          PORTS                               NAMES
1c6d250644c2   jjasonek/accounts:s4   "java -jar /accounts…"   43 seconds ago   Up 43 seconds   0.0.0.0:8080->8080/tcp              quirky_sutherland


## Dockerizing loans by Buildpacks

### build the image
PS C:\Training\Microservices\section4\accounts> cd ..\loans\
PS C:\Training\Microservices\section4\loans> mvn spring-boot:build-image
...
[INFO] Successfully built image 'docker.io/jjasonek/loans:s4'

PS C:\Training\Microservices\section4\loans> docker images
REPOSITORY                                 TAG       IMAGE ID       CREATED         SIZE
...
eazybytes/loans                            s4        5acf471ccc2d   45 years ago    310MB
paketobuildpacks/builder-jammy-java-tiny   latest    54ef03ee38f3   45 years ago    700MB

### note
size of the build image is 310MB in comparison with 503MB for accounts

### run the container
PS C:\Training\Microservices\section4\loans> docker run -d -p 8090:8090 jjasonek/loans:s4
beab93cb6b414c80df768d2a78f4a94b223df6abb4844a84bfcdb9805a7a7704


## Dockerizing loans by Buildpacks Google Jib

### build the image
PS C:\Training\Microservices\section4\loans> cd ..\cards\
PS C:\Training\Microservices\section4\cards> mvn compile jib:dockerBuild
...
[INFO] Built image to Docker daemon as jjasonek/cards:s4


PS C:\Training\Microservices\section4\cards> docker images
REPOSITORY                                 TAG       IMAGE ID       CREATED         SIZE
eazybytes/accounts                         s4        a25eb3d0b593   18 hours ago    503MB
...
jjasonek/loans                            s4        5acf471ccc2d   45 years ago    310MB
jjasonek/cards                            s4        040ba03ca6b8   55 years ago    351MB

### note 
The image is a bit bigger than the one built by buildpacks 
but still much smaller than the one built by Dockerfile approach

### run the container
PS C:\Training\Microservices\section4\cards> docker run -d --rm -p 9000:9000 jjasonek/cards:s4
9a33f9334f80280c92a984fcfabaa7ec95f98a3b1133086e22e78a93c7744e50


## Push image to Docker Hub
PS C:\Training\Microservices\section4\cards> docker image push docker.io/jjasonek/accounts:s4
The push refers to repository [docker.io/jjasonek/accounts]
f28814da67dd: Pushed
659a8c4ba776: Mounted from library/openjdk
0ac7ecf8a41c: Mounted from library/openjdk
d310e774110a: Mounted from library/openjdk
s4: digest: sha256:ca36b7f5455b68c41b5ac6db7fba05b49ac317d40db81202b15b24e0e8c446ac size: 1165

PS C:\Training\Microservices\section4\cards> docker image push docker.io/jjasonek/loans:s4

PS C:\Training\Microservices\section4\cards> docker image push docker.io/jjasonek/cards:s4


##  Compose file
We create inside the accounts project. It does not matter where it is placed. 
We just want to push it to the docker hub so it must be inside of one of the projects.

### Start the containers
PS C:\Training\Microservices\section4\cards> cd ..\accounts\
PS C:\Training\Microservices\section4\accounts> docker compose up -d
[+] Running 4/4
✔ Network accounts_eazybank  Created                                                                                                                                                                                                                                                                                                                                                0.1s
✔ Container cards-ms         Started                                                                                                                                                                                                                                                                                                                                                1.2s
✔ Container accounts-ms      Started                                                                                                                                                                                                                                                                                                                                                1.2s
✔ Container loans-ms         Started                                                                                                                                                                                                                                                                                                                                                1.2s
PS C:\Training\Microservices\section4\accounts>


### Stop the containers wit deleting them
PS C:\Training\Microservices\section4\accounts> docker compose down
[+] Running 4/4
✔ Container loans-ms         Removed                                                                                                                                                                                                                                                                                                                                                0.9s
✔ Container cards-ms         Removed                                                                                                                                                                                                                                                                                                                                                1.1s
✔ Container accounts-ms      Removed                                                                                                                                                                                                                                                                                                                                                1.0s
✔ Network accounts_eazybank  Removed                                                                                                                                                                                                                                                                                                                                                0.7s
PS C:\Training\Microservices\section4\accounts>

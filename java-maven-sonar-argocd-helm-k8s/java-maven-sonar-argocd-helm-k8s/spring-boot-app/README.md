# Spring Boot based Java web application
 
This is a simple Sprint Boot based Java application that can be built using Maven. Sprint Boot dependencies are handled using the pom.xml 
at the root directory of the repository.

This is a MVC architecture based application where controller returns a page with title and message attributes to the view.

## Execute the application locally and access it using your browser

Checkout the repo and move to the directory

```
git clone https://github.com/Venkateswaran1451/Jenkins-Zero-To-Hero/java-maven-sonar-argocd-helm-k8s/sprint-boot-app
cd java-maven-sonar-argocd-helm-k8s/sprint-boot-app
```

Execute the Maven targets to generate the artifacts

```
mvn clean package
```

The above maven target stroes the artifacts to the `target` directory. You can either execute the artifact on your local machine
(or) run it as a Docker container.

** Note: To avoid issues with local setup, Java versions and other dependencies, I would recommend the docker way. **


### Execute locally (Java 21 needed) and access the application on http://localhost:8080

```
java -jar target/spring-boot-web.jar
```

### The Docker way

Build the Docker Image

```
docker build -t eclipse-venkat-docker-agent:v3 .
```

```
docker run -d -p 8010:8080 -t eclipse-venkat-docker-agent:v3

 (saumya edit/ run this below, instead of above line)

 docker run -d -p 8010:8080 \
  -v "$PWD/target/spring-boot-web.jar:/app/app.jar" \
  eclipse-temurin:21-jdk \
  java -jar /app/app.jar

``` 

Hurray !! Access the application on `http://<ip-address>:8010`

     http://localhost:8010


## Next Steps

### Configure a Sonar Server locally

```
System Requirements
Java 21 (Oracle JDK, OpenJDK, or AdoptOpenJDK)
Hardware Recommendations:
   Minimum 2 GB RAM
   2 CPU cores
sudo apt update && sudo apt install unzip -y
adduser sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.9.0.112764.zip
unzip *
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-25.9.0.112764
chmod -R 775 /home/sonarqube/sonarqube-25.9.0.112764
cd /home/sonarqube/sonarqube-25.9.0.112764/bin/linux-x86-64 ## Depends on your architecture
./sonar.sh start
```

Hurray !! Now you can access the `SonarQube Server` on `http://<ip-address>:9000` 



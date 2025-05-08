# Udemy Course Microservices with Spring Boot, Docker, Kubernetes Section6
https://www.udemy.com/course/master-microservices-with-spring-docker-kubernetes/
## spring version: 3.4.4
## Java 21


## Documentation
After adding the library, the swagger page is accessible through address 
http://localhost:8080/swagger-ui/index.html,
http://localhost:8090/swagger-ui/index.html,
http://localhost:9000/swagger-ui/index.html.


## Configuration management

### Environment
This: envirinment.getProperty("JAVA_HOME")
gives output C:\Java\jdk-17.0.7 when I run the application from IntelliJ, 
which is strange since I changed the property to C:\Java\jdk-21.0.1

### @ConfigurationProperties
set of properties with prefix "accounts"

result of GET
{
    "message": "Welcome to EazyBank accounts related local APIs",
    "contactDetails": {
        "name": "John Doe - Developer",
        "email": "john@eazybank.com"
},
    "onCallSupport": [
        "(555) 555-1234",
        "(555) 523-1345"
    ]
}

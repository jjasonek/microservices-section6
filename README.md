# Udemy Course Microservices with Spring Boot, Docker, Kubernetes Section6
https://www.udemy.com/course/master-microservices-with-spring-docker-kubernetes/
## spring version: 3.4.5
## Java 21


## Documentation
After adding the library, the swagger page is accessible through address 
http://localhost:8080/swagger-ui/index.html,
http://localhost:8090/swagger-ui/index.html,
http://localhost:9000/swagger-ui/index.html.


## Configuration management v1-springboot

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



## Configuration management v2-spring-cloud-config

## classpath approach
After start of the Configserver we can se properties from urls beginning with localhost:8071
For example localhost:8071/accounts/prod gives:
{
    "name": "accounts",
    "profiles": [
    "prod"
    ],
    "label": null,
    "version": null,
    "state": null,
    "propertySources": [
        {
            "name": "classpath:/config/accounts-prod.yml",
            "source": {
                "build.version": "1.0",
                "accounts.message": "Welcome to EazyBank accounts related prod APIs",
                "accounts.contactDetails.name": "Reine Aishwarya - Product Owner",
                "accounts.contactDetails.email": "aishwarya@eazybank.com",
                "accounts.onCallSupport[0]": "(453) 392-4829",
                "accounts.onCallSupport[1]": "(236) 203-0384"
            }
        },
        {
            "name": "classpath:/config/accounts.yml",
            "source": {
                "build.version": "3.0",
                "accounts.message": "Welcome to EazyBank accounts related local APIs",
                "accounts.contactDetails.name": "John Doe - Developer",
                "accounts.contactDetails.email": "john@eazybank.com",
                "accounts.onCallSupport[0]": "(555) 555-1234",
                "accounts.onCallSupport[1]": "(555) 523-1345"
            }
        }
    ]
}

### Starting the accounts application with the qa profile:
2025-05-13T15:08:16.595+02:00  INFO 14392 --- [accounts] [  restartedMain] c.e.accounts.AccountsApplication         : The following 1 profile is active: "qa"
2025-05-13T15:08:16.643+02:00  INFO 14392 --- [accounts] [  restartedMain] o.s.c.c.c.ConfigServerConfigDataLoader   : Fetching config from server at : http://localhost:8071
2025-05-13T15:08:16.643+02:00  INFO 14392 --- [accounts] [  restartedMain] o.s.c.c.c.ConfigServerConfigDataLoader   : Located environment: name=accounts, profiles=[default], label=null, version=null, state=null
2025-05-13T15:08:16.643+02:00  INFO 14392 --- [accounts] [  restartedMain] o.s.c.c.c.ConfigServerConfigDataLoader   : Fetching config from server at : http://localhost:8071
2025-05-13T15:08:16.643+02:00  INFO 14392 --- [accounts] [  restartedMain] o.s.c.c.c.ConfigServerConfigDataLoader   : Located environment: name=accounts, profiles=[qa], label=null, version=null, state=null


### Retrieving the properties from the Configserver I can see the following:
2025-05-13T21:10:03.776+02:00  INFO 40280 --- [configserver] [nio-8071-exec-1] o.s.c.c.s.e.NativeEnvironmentRepository  : Adding property source: Config resource 'file [C:\Users\jijas\AppData\Local\Temp\config-repo-11059085055692324310\accounts.yml]' via location 'file:/C:/Users/jijas/AppData/Local/Temp/config-repo-11059085055692324310/'
2025-05-13T21:10:03.778+02:00  WARN 40280 --- [configserver] [nio-8071-exec-1] o.s.c.c.s.e.CipherEnvironmentEncryptor   : Cannot decrypt key: accounts.contactDetails.email (class java.lang.UnsupportedOperationException: No decryption for FailsafeTextEncryptor. Did you configure the keystore correctly?)
2025-05-13T21:10:04.309+02:00  WARN 40280 --- [configserver] [nio-8071-exec-2] o.s.c.c.s.e.EnvironmentController        : Error getting the Environment with name=.well-known profiles=appspecific label=com.chrome.devtools.json includeOrigin=false
...
Caused by: org.eclipse.jgit.api.errors.RefNotFoundException: Ref com.chrome.devtools.json cannot be resolved

I think the reason is settings in the GitHub side.
Like email in accounts-prod.yml 


## Refreshing the config properties

### Now we cen see, our properties on the Config Server are updating.
Like here: http://localhost:8071/accounts/prod

### To refresh properties for the microservices without restarting them 
we need to call the actuator refresh endpoint: 
POST http://localhost:8080/actuator/refresh for the accounts.

And the response is:
[
    "config.client.version",
    "accounts.message"
]

2025-05-14T16:03:04.606+02:00  INFO 30468 --- [configserver] [nio-8071-exec-6] o.s.c.c.s.e.NativeEnvironmentRepository  : Adding property source: Config resource 'file [C:\Users\jijas\AppData\Local\Temp\config-repo-2333009254686404261\accounts.yml]' via location 'file:/C:/Users/jijas/AppData/Local/Temp/config-repo-2333009254686404261/'
2025-05-14T16:03:05.135+02:00  INFO 30468 --- [configserver] [nio-8071-exec-7] o.s.c.c.s.e.NativeEnvironmentRepository  : Adding property source: Config resource 'file [C:\Users\jijas\AppData\Local\Temp\config-repo-2333009254686404261\accounts-prod.yml]' via location 'file:/C:/Users/jijas/AppData/Local/Temp/config-repo-2333009254686404261/'
2025-05-14T16:03:05.135+02:00  INFO 30468 --- [configserver] [nio-8071-exec-7] o.s.c.c.s.e.NativeEnvironmentRepository  : Adding property source: Config resource 'file [C:\Users\jijas\AppData\Local\Temp\config-repo-2333009254686404261\accounts.yml]' via location 'file:/C:/Users/jijas/AppData/Local/Temp/config-repo-2333009254686404261/'

2025-05-14T16:03:04.058+02:00  INFO 44180 --- [accounts] [nio-8080-exec-3] o.s.c.c.c.ConfigServerConfigDataLoader   : Fetching config from server at : http://localhost:8071
2025-05-14T16:03:04.608+02:00  INFO 44180 --- [accounts] [nio-8080-exec-3] o.s.c.c.c.ConfigServerConfigDataLoader   : Located environment: name=accounts, profiles=[default], label=null, version=082cd1ce7f42f9a5b0d918fb959d46729883a52d, state=
2025-05-14T16:03:04.621+02:00  INFO 44180 --- [accounts] [nio-8080-exec-3] o.s.c.c.c.ConfigServerConfigDataLoader   : Fetching config from server at : http://localhost:8071
2025-05-14T16:03:05.137+02:00  INFO 44180 --- [accounts] [nio-8080-exec-3] o.s.c.c.c.ConfigServerConfigDataLoader   : Located environment: name=accounts, profiles=[prod], label=null, version=082cd1ce7f42f9a5b0d918fb959d46729883a52d, state=
2025-05-14T16:03:06.173+02:00  INFO 44180 --- [accounts] [nio-8080-exec-3] o.s.cloud.commons.util.InetUtils         : Cannot determine local hostname
2025-05-14T16:03:06.176+02:00  INFO 44180 --- [accounts] [nio-8080-exec-3] .e.DevToolsPropertyDefaultsPostProcessor : Devtools property defaults active! Set 'spring.devtools.add-properties' to 'false' to disable
2025-05-14T16:03:06.397+02:00  INFO 44180 --- [accounts] [nio-8080-exec-3] o.s.cloud.endpoint.RefreshEndpoint       : Refreshed keys : [config.client.version, accounts.message]



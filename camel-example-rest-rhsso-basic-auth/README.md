# Setup

Currently based on this [official doc](https://access.redhat.com/documentation/en-us/red_hat_single_sign-on/7.2/html-single/securing_applications_and_services_guide/index#spring_boot_adapter)

## Adapter Installation

The Keycloak Spring Boot adapter takes advantage of Spring Boot’s autoconfiguration so all you need to do is add the Keycloak Spring Boot starter to your project. They Keycloak Spring Boot Starter is also directly available from the Spring Start Page. To add it manually using Maven, add the following to your dependencies:
```
<dependency>
    <groupId>org.keycloak</groupId>
    <artifactId>keycloak-spring-boot-starter</artifactId>
</dependency>
```
Add the Adapter BOM dependency:

```
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.keycloak.bom</groupId>
      <artifactId>keycloak-adapter-bom</artifactId>
      <version>9.0.13.redhat-00006</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```
## BOM Versions
For this example the versions used are:

* keycloak-adapter-bom: ```9.0.13.redhat-00006```
* fuse-springboot-bom: ```7.8.0.fuse-sb2-780048-redhat-00001```

Currently the following embedded containers are supported and do not require any extra dependencies if using the Starter:

* Tomcat
* Undertow
* Jetty

## Required Spring Boot Adapter Configuration

This section describes how to configure your Spring Boot app to use Keycloak.

Instead of a keycloak.json file, you configure the realm for the Spring Boot Keycloak adapter via the normal Spring Boot configuration. For example:
```
keycloak.realm = basic-auth
keycloak.auth-server-url = http://127.0.0.1:8080/auth
keycloak.ssl-required = external
keycloak.resource = basic-auth-service
keycloak.enable-basic-auth = true
keycloak.credentials.secret = password
```

You can disable the Keycloak Spring Boot Adapter (for example in tests) by setting keycloak.enabled = false.

To configure a Policy Enforcer, unlike keycloak.json, policy-enforcer-config must be used instead of just policy-enforcer.

You also need to specify the Java EE security config that would normally go in the web.xml. The Spring Boot Adapter will set the login-method to KEYCLOAK and configure the security-constraints at startup time. Here’s an example configuration:
```
keycloak.securityConstraints[0].authRoles[0] = user
keycloak.securityConstraints[0].securityCollections[0].name = secure api
keycloak.securityConstraints[0].securityCollections[0].patterns[0] = /camel/select/*
```

# Setup RHSSO

1. Download and start RHSSO
2. As ```admin``` import the realm file  located in ```realm/basic-auth-realm.json```

# Test
                                                                   
Now your endpoint is protected with basic auth: 

```curl  --user admin:password  http://localhost:8282/camel/select/12313123```
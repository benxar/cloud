## Cohort20 :: Sillybank Springboot App ##

----------

#### OCP deployment ####

```
 $ oc new-project sillybank

 $ oc secrets new-basicauth gitlab --username=USERNAME --password=PASSWORD!
 $ oc secrets link builder gitlab

 $ oc new-app --name='sillybank' -l name='sillybank' \
  openshift/jboss-eap70-openshift:1.5~https://USERNAME@gitlab.consulting.redhat.com/americas-tech-exchange-2017/container-cohort20/sillybank.git#development \
  --context-dir=app/springboot-sample

 $ oc set build-secret bc/sillybank --source gitlab
 $ oc expose svc/sillybank
```

#### WebLogic 2 OpenShift migration tips ####

1. Change the packaging to deploy the app at /
```
<project>
 ...
 <packaging>war</packaging>
 ...
 <build>
  <plugins>
    ...
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-war-plugin</artifactId>
      <version>2.3</version>
      <configuration>
        <failOnMissingWebXml>false</failOnMissingWebXml>
        <outputDirectory>target</outputDirectory>
        <warName>ROOT</warName>
      </configuration>
    </plugin>
  </plugins>
 </build>
 ...
</project>
```

2. Be sure to exclude tomcat-embed-websocket dependency in order to avoid collisions with undertow
```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
  <scope>compile</scope>
  <exclusions>
    <exclusion>
      <groupId>org.apache.tomcat.embed</groupId>
      <artifactId>tomcat-embed-websocket</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

3. Rearrange project's structure into an standard Maven webapp

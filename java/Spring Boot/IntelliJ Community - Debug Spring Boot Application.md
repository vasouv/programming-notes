## Option 1
```xml
<plugins>
    <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
            <fork>false</fork>
        </configuration>
    </plugin>
</plugins>
```

## Option 2
```xml
<properties>
    <!-- For using breakpoint in Debug mode -->
    <spring-boot.run.fork>false</spring-boot.run.fork>
    <!-- Other Properties -->
</properties>
```
## Option 3
Create new Maven run configuration and for the command line `spring-boot:run -Dfork=false`
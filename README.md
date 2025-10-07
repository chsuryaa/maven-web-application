# Sample Maven Web Application (multi-java)

## Purpose
- Small WAR webapp that works across Java 8/11/17 builds.
- Ready for Jenkins builds and Nexus deployment.
- Instructions included to deploy via Jenkins "Deploy to Container" (Tomcat Manager).

## Build locally
Default builds with Java 8 compatibility:
```
mvn clean package
```
To build with Java 11 profile:
```
mvn clean package -Pjava11
```
To build with Java 17 profile:
```
mvn clean package -Pjava17
```

## Artifact
After build:
```
target/web-application.war
```

## Nexus
Update `pom.xml` distributionManagement with your Nexus hostname or IP and add credentials to Jenkins/Maven settings.xml:
`/var/lib/jenkins/.m2/settings.xml` (example):
```xml
<settings>
  <servers>
    <server>
      <id>nexus-releases</id>
      <username>YOUR_NEXUS_USER</username>
      <password>YOUR_NEXUS_PASSWORD</password>
    </server>
    <server>
      <id>nexus-snapshots</id>
      <username>YOUR_NEXUS_USER</username>
      <password>YOUR_NEXUS_PASSWORD</password>
    </server>
  </servers>
</settings>
```

Use `mvn clean deploy` in Jenkins to upload to Nexus.

## Jenkins - Deploy to Tomcat (Deploy to container plugin)
1. Install "Deploy to container" plugin in Jenkins.
2. In Jenkins job, under Post-build Actions: "Deploy war/ear to container".
   - WAR/EAR: `target/web-application.war`
   - Containers: Select "Tomcat 8.x/9.x/10.x" (choose the one matching your Tomcat)
   - Credentials: Add Tomcat Manager credentials (username/password)
   - Manager URL: `http://<TOMCAT_HOST>:8080/manager/text`
3. Save and run the job. Plugin will use Tomcat Manager to deploy the WAR.

## Notes on compatibility
- The project sets default Java source/target to 1.8 for wide compatibility.
- Use the `-Pjava11` or `-Pjava17` profiles in Jenkins if your agent runs those JDKs.
- Servlet API is provided by the container (Tomcat).

## Troubleshooting
- If you see `Unable to load mojo` errors, ensure maven-war-plugin version >= 3.2.3 is used (this project uses 3.4.0).
- If Jenkins agents have multiple JDKs, configure the correct JDK in the Jenkins job (Manage Jenkins -> Global Tool Configuration -> JDK installations).


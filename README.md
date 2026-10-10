# Java Web App: CI/CD with Maven, SonarQube and Jenkins or GitHub Actions

A Java web application (`MyWebApp`, packaged as a WAR) with a Hello service and a
JSP page. The project includes two CI/CD pipelines: a Jenkins pipeline and a GitHub
Actions workflow. Both build the app with Maven, run its tests, scan it with
SonarQube, and check the quality gate.

## Project structure

```
.
├── MyWebApp/                       # Maven project
│   ├── pom.xml                     # Build configuration (WAR packaging)
│   ├── src/main/java/com/example/service/HelloService.java
│   ├── src/main/webapp/index.jsp   # Page
│   ├── src/main/webapp/WEB-INF/web.xml
│   └── src/test/java/com/example/service/HelloServiceTest.java
├── Jenkinsfile                     # Jenkins pipeline
├── .github/workflows/cicd.yml      # GitHub Actions workflow
└── sample.txt
```

## Run locally

Requirements: JDK 17 (the POM compiles for Java 17), Maven 3.

```bash
mvn -f MyWebApp/pom.xml clean install
```

This compiles the code, runs the tests and produces `MyWebApp/target/MyWebApp.war`.

## Pipelines

### Jenkins (`Jenkinsfile`)

1. **Build:** `mvn clean install` for `MyWebApp/pom.xml`.
2. **Unit tests and coverage:** publishes JUnit results and JaCoCo coverage.
3. **Code analysis:** runs `mvn sonar:sonar` inside the `SonarQube` server configuration.
4. **Quality gate:** waits for SonarQube's result and stops the pipeline if the gate fails.

Jenkins needs a Maven tool named `Maven3`, a SonarQube server named `SonarQube`,
and the JaCoCo and JUnit plugins.

### GitHub Actions (`.github/workflows/cicd.yml`)

Runs on push to `main` or on manual trigger, using a self-hosted runner.

1. Checks out the code and sets up JDK 17.
2. Builds with Maven.
3. Runs the SonarQube scan and checks the quality gate.
4. Sends a Slack notification through a webhook.

## Secrets and settings the pipelines need

| Name | Used by | Purpose |
|------|---------|---------|
| `SONAR_TOKEN` | GitHub Actions | Authenticates the SonarQube scan |
| `SONAR_HOST_URL` | GitHub Actions | URL of the SonarQube server |
| `SLACK_WEBHOOK_URL` | GitHub Actions | Slack notifications |

Store these as repository secrets. Do not put them in the workflow file.

The workflow sets `-Dsonar.organization=my-org` and `-Dsonar.projectKey=my-Java-we-app`.
Replace these placeholders with your own SonarQube organisation and project key.

## Notes

- The `target/` folders are build output and are excluded in `.gitignore`.
- The Jenkins pipeline does not set a JDK itself. It uses whatever Java the agent has installed, so make sure that is JDK 17 too.
- The GitHub Actions workflow still uses `sonarsource/sonarqube-scan-action@master` and `sonarqube-quality-gate-action@master`. Pin these to a released version for repeatable builds.

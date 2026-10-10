# MyWebApp

The Maven web application (WAR) that the CI/CD pipelines in the parent repository build.

| Path | Contents |
|------|----------|
| `pom.xml` | Maven build: WAR packaging, SonarQube and JaCoCo settings |
| `src/main/java/com/example/service/HelloService.java` | Returns a greeting for a name |
| `src/test/java/com/example/service/HelloServiceTest.java` | JUnit test for `HelloService` |
| `src/main/webapp/index.jsp` | Home page |
| `src/main/webapp/WEB-INF/web.xml` | Servlet deployment descriptor |

Build and test from the repository root:

```bash
mvn -f MyWebApp/pom.xml clean install
```

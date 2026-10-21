# Lab 3: Creating a Docker Image for a Java Application

Application: [CalculatorMavenApp](https://github.com/Ibrahim-Adel15/CalculatorMavenApp.git)

## Part 1: Build Inside Docker (app1)

**Dockerfile.part1**
```dockerfile
FROM maven:3.9-eclipse-temurin-17
RUN useradd -m appuser
WORKDIR /app
COPY . .
RUN chown -R appuser:appuser /app
USER appuser
RUN mvn package
EXPOSE 8080
CMD ["java", "-jar", "target/calculator.jar"]
```

**Commands**
```bash
docker build -f Dockerfile.part1 -t app1 .
docker images app1
docker run -it --name container1 app1
docker rm -f container1
docker rmi app1
```

**Result:** the application ran successfully (inputs 8 and 50):
```
Sum: 58.0
Subtract: -42.0
Multiply: 400.0
Divide: 0.16
```
**Image size (app1): 814 MB** (content size 251 MB)

![app1](images/app1.png)

## Part 2: Build Locally (app2)

**Build the JAR locally**
```bash
mvn package    # generates target/calculator.jar (5 tests passed)
```

**Dockerfile.part2**
```dockerfile
FROM eclipse-temurin:17-jre
RUN useradd -m appuser
WORKDIR /app
COPY target/calculator.jar app.jar
RUN chown -R appuser:appuser /app
USER appuser
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```

**Commands**
```bash
docker build -f Dockerfile.part2 -t app2 .
docker images app2
docker run -it --name container2 app2
docker rm -f container2
docker rmi app2
```

**Result:** same output as Part 1 (Sum: 58.0, Subtract: -42.0, Multiply: 400.0, Divide: 0.16)

**Image size (app2): 427 MB** (content size 110 MB)

![app2](images/app2.png)

## Bonus: Image Size Comparison

| Image | Approach | Base Image | Size (disk usage) | Content size |
|-------|----------|------------|-------------------|--------------|
| app1 | Build inside Docker | maven:3.9-eclipse-temurin-17 | **814 MB** | 251 MB |
| app2 | Build locally, copy JAR | eclipse-temurin:17-jre | **427 MB** | 110 MB |

app2 is about **387 MB (~48%) smaller** than app1.

### Explanation
- **app1 is larger** because the final image still contains everything used to build the app: Maven, the full JDK, the source code, and the downloaded dependencies. None of these are needed to run the JAR.
- **app2 is smaller** because it only contains a JRE and the final JAR.
- **Reproducibility:** app1 builds the same way on any machine (only Docker is needed). app2 depends on Java and Maven being installed locally.
- **Security:** a smaller image has fewer tools and a smaller attack surface.
- **Best practice:** a multi-stage build combines both advantages (build in a Maven stage, copy only the JAR into a JRE stage).

## Notes
- Both Dockerfiles create a **non-root user** (`appuser`) with `useradd` and switch to it with `USER`, so the app does not run as root.
- The app is a console calculator (reads input with `Scanner`), so it is run with `-it`. `EXPOSE 8080` documents the port as required by the lab.
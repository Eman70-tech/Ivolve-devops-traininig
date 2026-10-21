# Lab 1: Building and Packaging a Java Application with Maven

## Steps
```bash
sudo apt update && sudo apt install -y openjdk-17-jdk maven
git clone https://github.com/Ibrahim-Adel15/CalculatorMavenApp.git
cd CalculatorMavenApp
mvn test                         # run unit tests
mvn package                      # artifact: target/calculator.jar
java -jar target/calculator.jar  # run the app
```


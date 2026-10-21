# Lab 2: Building and Packaging a Java Application with Gradle

## Steps
```bash
# Gradle installed with SDKMAN (the apt version is too old)
sudo apt install -y zip unzip curl
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk install gradle

git clone https://github.com/Ibrahim-Adel15/CalculatorGradleApp.git
cd CalculatorGradleApp
gradle test                          # run unit tests
gradle build                         # artifact: build/libs/calculator.jar
java -jar build/libs/calculator.jar  # run the app
```

## Issue faced
`gradle test` failed with `Could not find method java()` because the Gradle
from `apt` (4.4.1) is too old for this `build.gradle`. Fixed by installing a
newer Gradle with SDKMAN.


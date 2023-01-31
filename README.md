# TP2 : Découverte Github Actions

> Note : What is it supposed to do?

> Note : Unit tests? Component tests?


### 2-1 What are testcontainers ?

### 2-2 Document your Github Actions configurations.

```yml
name: CI devops 2023
on:
  #to begin you want to launch this job in main and develop
  push:
    branches:
     - TP2
  pull_request:

jobs:
  test-backend: 
   runs-on: ubuntu-22.04
   steps:
    #checkout your github code using actions/checkout@v2.5.0
    - uses: actions/checkout@v2.5.0

    #do the same with another action (actions/setup-java@v3) that enable to setup jdk 17
    - name: Set up JDK 17
      uses: actions/setup-java@v3
      with:
        java-version: 17
        distribution: "adopt"

    #finally build your app with the latest command
    - name: Build and test with Maven
      #run: mvn clean verify --file api/pom.xml
      run : mvn -B verify sonar:sonar -Dsonar.projectKey=MaximeBattu_DevOps -Dsonar.organization=maximebattu -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${{ secrets.SONAR_TOKEN }} --file ./simple-api/pom.xml

```

> Note : Secured Variables, why ?

> Note : Why did we put needs: build-and-test-backend on this job? Maybe try without this and you will see !

> Note : For what purpose do we need to push docker images ?


### Document your quality gate configuration.

> Tip : You can use on: workflow_run to trigger a workflow when another workflow is passed.

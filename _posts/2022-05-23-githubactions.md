---
title: CI/CD pipleline with githubactions
date: 2022-05-23 12:00:00 -500
categories: [Springboot, docker]
tags: [Github Actions, Springboot, CI/CD]
---

This article introduces the concept of CI/CD pipeline using Github Actions using the framework fo java (i.e. Spring Boot).

#1. Prerequisites:

    1.1 GithubAccount
    1.2 DockerHub Account
    1.3 Java 11 (You can also use 8 or higher than that)
    1.4 Spring Boot
    1.5 Compiler of your choice (I used intellij)
    1.6 Maven

#2. Steps to follow

## 2.1 Create a spring boot application

Let's dive into the steps to be followed. First of all, create a spring boot application with the help of <https://start.spring.io>.

Since our main goal, is to learn about the concept of CI/CD, using the github action, let's just do a few changes, in the spring boot application we
created earlier.

Perform the following changes in your main application located in /src/main/java/(your package name).
In my case,I named it as CicdGithubActionApplication.

```javascript
@SpringBootApplication
@RestController
public class CicdGithubActionApplication {

  @GetMapping("/welcome")
  public String welcome() {
    return "Welcome to my first article";
  }

  public static void main(String[] args) {
  SpringApplication.run(CicdGithubActionApplication.class, args);
}
}
```

## 2.2 Push the application into github

```javascript
1. Create a new repository in github using the New button.
2. After that give a repository name and click create repository.
3. Goto your terminal of your compiler where you run your spring boot application.
4. Then type the following commands :
        * git init
        * git add .
        * git commit -m "first commit"
        * git remote add origin https://github.com/(user git username)/(your git repository).git
        * git push -u origin main

```
5. After this your application is pushed to github.
6. You can see click a button called Action inside that repository you recently pushed.
7. You can select a Java with maven workflow.
8. You will move to a .yml file where changes need to be made. I did the following changes.

```javascript

name: project cicd

on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'temurin'
        cache: maven
    - name: Build with Maven
      run: mvn clean install

```
9. Then after this Click on Start Commit button and create a pull request for this .yml file.
10. If you created the file using a different branch you need to merge it to master.
11. In your spring boot application, take pull from the latest changes and you can see a new folder .github where that .yml file is present.
12. Now you need to create a respository in the docker hub for your image.
13. After that add following changes into the existing .yml file so that workflow will automate the building and pushing the image into dockerhub.

```javascript
  - name: Build & Push Docker image
      uses: mr-smithers-excellent/docker-build-push@v5
      with:
        image: (your docker hub username)/(your docker hub respository image)
        tags: latest
        registry: docker.io
        dockerfile: Dockerfile
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}
```
14. For the secrets you can go to your respository and add the secrets like the following ways:
    * Go to the settings of that repository and in the left side you will find a Secrets option inside the Security bar.
    * Click on New Repository secret
    * Use DOCKER_USERNAME in the Name* field and use your docker username in Secret* field.
    * User DOCKER_PASSWORD in the Name* field and use your docker password in Secret* field.

15. Now after all the changes you can commit and push it in github and go to action tab and we can see the build process and how the automation is carried out using the github actions.
The final result of automation process with build & test, build docker iamge and push it to docker hub will look like the images below.

### Build Process:
<img src="/assets/build process.png" alt="" style="height: 500px; width:900px;"/>

### Pushing images to docker hub:
<img src="/assets/push to docker.png" alt="" style="height: 500px; width:900px;"/>


### Pushed images in docker hub repository:
<img src="/assets/docker hub.png" alt="" style="height: 500px; width:900px;"/>

### Workflow success:
<img src="/assets/build success.png" alt="" style="height: 500px; width:900px;"/>

### Pulling images from docker hub:
<img src="/assets/docker pull.png" alt="" style="height: 500px; width:900px;"/>

### Running the image:
<img src="/assets/docker run.png" alt="" style="height: 500px; width:900px;"/>

### Final Result after the application runs using docker into the port 8080 :
<img src="/assets/result.png" alt="" style="height: 500px; width:900px;"/>

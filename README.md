# .gitlab-ci.yml

stages:
  - build
  - test
  - deploy

variables:
  MAVEN_CLI_OPTS: "-s .m2/settings.xml -B"
  MAVEN_CLI: "mvn ${MAVEN_CLI_OPTS}"

cache:
  paths:
    - .m2/repository

build:
  stage: build
  image: maven:3.8.4-openjdk-17
  script:
    - $MAVEN_CLI clean package
  artifacts:
    paths:
      - target/*.jar

test:
  stage: test
  image: maven:3.8.4-openjdk-17
  script:
    - $MAVEN_CLI test

deploy:
  stage: deploy
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:latest .
    - docker tag myapp:latest myrepo/myapp:latest
    - docker push myrepo/myapp:latest
  only:
    - main


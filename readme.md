## Spring-Petclinic Jenkins Pipeline

This document, explains the Jenkins pipeline defined in the `Jenkinsfile` for the Spring Petclinic application. The pipeline automates various tasks related to building, testing, deploying, and securing the application.

* Building and testing the application
* Analyzing code quality and security
* Packaging and deploying the application

**Prerequisites:**

* A Jenkins server up and running
* Docker installed on the Jenkins server (or a Docker daemon accessible)
* JFrog Artifactory instance configured
* JFrog Xray connected to artifactory instance
* A Git repository containing the Spring Petclinic source code (https://github.com/spring-projects/spring-petclinic)
* Maven installed on the Jenkins server
* SonarQube server configured 
* Snyk account and credentials 
* Docker Hub account and credentials

**Environment Variables:**

The Jenkinsfile uses several environment variables to configure the build process. You'll need to define these variables in your Jenkins configuration for the pipeline to work correctly.

* `PROJECT_NAME`: Name of your project (default: 'petclinic')
* `DOCKER_ID`: Your Docker Hub username (if pushing to Docker Hub)
* `MAVEN_HOME`: Path to the Maven installation on the Jenkins server (e.g., '/usr/local/maven')
* `IMAGE_NAME`: Name of the Docker image to build (default: 'spring-petclinic')
* `DOCKER_REGISTRY`: URL of your Docker registry (e.g., 'http://localhost:8082/artifactory')
* `ARTIFACTORY_ACCESS_TOKEN`: Access token for JFrog Artifactory
* `CHECKSTYLE_REPORT`: Name of the Checkstyle report file (default: 'checkstyle-result.xml')
* `JACOCO_REPORT`: Name of the JaCoCo code coverage report file (default: 'target/site/jacoco/jacoco.xml')
* `SONAR_URL`: URL of your SonarQube server (default: 'http://localhost:9000') (optional)
* `SNYK_TOKEN`: Snyk API token (optional)
* `SNYK_ORG_ID`: Snyk organization ID (optional)

**Pipeline Stages:**

The pipeline is divided into several stages, each performing a specific task in the build and deployment process:

1. **Clean:** Removes any existing Spring Petclinic directory from the Jenkins workspace to ensure a clean build environment.
2. **Clone Git repository:** Downloads the latest codebase of the Spring Petclinic application from the specified Git repository.
3. **Checkstyle:** Runs Checkstyle, a static code analysis tool, to identify potential coding style violations in the project's source code. This helps maintain code consistency and readability.
4. **SAST Scan with Snyk Code:** Performs a security scan using Snyk to detect vulnerabilities in the code. This helps identify and address potential security risks before deployment.
5. **SCA Scan with Snyk**: Analyzes open-source dependencies for vulnerabilities.
6. **Compile:** Compiles the Spring Petclinic source code into a JAR (Java Archive) file using Maven, a build automation tool for Java projects. This stage doesn't run any tests.
7. **Unit Tests & Code Coverage:** Executes the unit tests for the Spring Petclinic application using Maven. Additionally, it generates a JaCoCo code coverage report that shows which parts of the code are exercised by the tests. This helps ensure the quality and reliability of the application. 
8. **Code Coverage:** Uses the JaCoCo plugin within Jenkins to publish the generated code coverage reports. This provides insights into the effectiveness of your unit tests.
9. **SonarQube Analysis:** Performs a code quality analysis using SonarQube, a platform for continuous inspection and improvement of code. This stage is optional and requires a SonarQube server to be configured.
10. **Docker Build:** Creates a Docker image for the Spring Petclinic application. This image can be used to package and deploy the application in a consistent and portable manner.
11. **Upload to maven artifacts to JF Artifactory:** Uploads the generated JAR file to JFrog Artifactory, a repository manager for storing and distributing software artifacts.
12. **Upload docker image to JF Artifactory:** Scans and pushes the Docker image to JFrog Artifactory, providing a central location for managing Docker images.
13. **Upload to Dockerhub:** Pushes the newly built Docker image to a Docker registry, such as Docker Hub. This allows you to store and share the image for easy deployment.


**Running the Pipeline:**

1. Save the `Jenkinsfile` content in your Jenkins project workspace.
2. Configure the environment variables mentioned above in your Jenkins job configuration.
3. Make any necessary modifications to the pipeline stages based on your specific needs (e.g., enabling/disabling stages, adjusting credentials).
4. Run the Jenkins job to trigger the pipeline execution.

***Docker image execution*** 

- Prerequisite
    - Device operating system: Mac, Linux, or Windows
    - Docker version 25+
- Download the docker image
`````
docker pull ndadmin888/spring-petclinic:latest
`````
- Execute the container
`````
docker run -d --name spring-petclinic -p 7080:8080 ndadmin888/spring-petclinic:latest
`````
    - NOTE: Update port '7080', if any other service is currently running.
- Open browser with url: [http://localhost:7080](http://localhost:7080)
- Stop the container
`````

**Additional Notes:**

* The pipeline uses credentials for accessing JFrog Artifactory, Docker Hub, and SonarQube. Configure these credentials in Jenkins.
* You can comment out or uncomment stages based on your requirements.
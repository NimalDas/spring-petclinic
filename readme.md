## spring-petclinic Jenkins Pipeline (README.md)

This document, written in Markdown language, explains the Jenkins pipeline defined in the `Jenkinsfile` for the Spring Petclinic application. The pipeline automates various tasks related to building, testing, deploying, and securing the application.

**Prerequisites:**

* A Jenkins server up and running
* Docker installed on the Jenkins server (or a Docker daemon accessible)
* JFrog Artifactory instance configured
* A Git repository containing the Spring Petclinic source code (https://github.com/spring-projects/spring-petclinic)
* Maven installed on the Jenkins server
* SonarQube server configured (optional)
* Snyk account and credentials (optional)
* Docker Hub account and credentials (optional)

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

The pipeline is divided into several stages:

1. **Clone Git repository:** Clones the Spring Petclinic source code from a Git repository.
2. **Clean (optional):** Deletes any existing Spring Petclinic directory to ensure a clean build.
3. **Checkstyle:** Runs Checkstyle code analysis to identify potential style violations.
4. **SAST Scan with Snyk Code (optional):** Performs a security scan using Snyk to detect vulnerabilities in the code. (Currently commented out)
5. **Compile:** Compiles the source code without running tests.
6. **Unit Tests & Code Coverage (optional):** Runs unit tests and generates a JaCoCo code coverage report. (Currently commented out)
7. **Code Coverage:** Uses the JaCoCo plugin to publish code coverage reports.
8. **SonarQube Analysis (optional):** Performs code quality analysis using SonarQube.
9. **Docker Build:** Builds a Docker image for the Spring Petclinic application.
10. **Docker Push:** Pushes the Docker image to a Docker registry (Docker Hub or JFrog Artifactory).
11. **Upload to maven artifacts to JF Artifactory:** Uploads the generated JAR file to JFrog Artifactory.
12. **Upload docker image to JF Artifactory:** Scans and pushes the Docker image to JFrog Artifactory.

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

* The pipeline uses credentials for accessing JFrog Artifactory, Docker Hub (if applicable), and SonarQube (if applicable). Configure these credentials in Jenkins.
* You can comment out or uncomment stages based on your requirements.




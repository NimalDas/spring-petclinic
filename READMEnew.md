# Jenkins Pipeline for Spring PetClinic Project

This Jenkins pipeline provides a series of stages for continuous integration and continuous deployment (CI/CD) of the Spring PetClinic project.

## Environment Variables

The pipeline uses the following environment variables:

- `MAVEN_HOME`: Path to the Maven tool.
- `DOCKER_REGISTRY`: URL of the Docker registry.
- `DOCKER_IMAGE`: Name of the Docker image.
- `JFROG_REPO`: Name of the JFrog repository.
- `CHECKSTYLE_REPORT`: Path to the Checkstyle report.
- `JACOCO_REPORT`: Path to the JaCoCo report.
- `SONAR_URL`: URL of the SonarQube instance.

## Stages

The pipeline includes the following stages:

1. **Clone Git repository**: Clones the Spring PetClinic repository from GitHub.
2. **Compile**: Compiles the project using Maven.
3. **Checkstyle**: Checks the code style using Checkstyle.
4. **Code Coverage**: Generates a code coverage report using JaCoCo.
5. **SonarQube Analysis**: Performs a SonarQube analysis of the code.
6. **Docker Build**: Builds a Docker image of the project.
7. **Docker Push**: Pushes the Docker image to a Docker registry.
8. **Upload Binaries to JFrog** (commented out): Uploads the project binaries to a JFrog Artifactory repository.

## Usage

To use this Jenkinsfile, place it in the root directory of your project and configure a Jenkins job to use it. You may need to adjust the environment variables and stage steps to match your specific environment and requirements.

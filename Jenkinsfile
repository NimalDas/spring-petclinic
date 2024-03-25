pipeline {
    agent any
    environment {
        dockerRegistryOrg = "ndadmin888"
        MAVEN_HOME = tool 'maven'
        CHECKSTYLE_REPORT = 'checkstyle-result.xml'
        JACOCO_REPORT = 'target/site/jacoco/jacoco.xml'
    }

    stages {
        stage('Clean') {
            steps {
                sh "rm -rf spring-petclinic" // Clean the workspace
            }
        }

        stage('Clone Github repository') {
            steps {
                git branch: 'main', url: 'https://github.com/NimalDas/spring-petclinic.git'
            }
        }

        stage('Compile') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn clean package -DskipTests=true" // Compile code without running tests
            }
        }

        stage('Checkstyle') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn checkstyle:checkstyle" // Run code style checks
                junit allowEmptyResults: true, testResults: "**/${CHECKSTYLE_REPORT}" // (Optional) JUnit reporting for Checkstyle (may not be applicable)
            }
        }

        stage('Unit Test and Code Coverage') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn test jacoco:report" // Run unit tests and generate code coverage report
                junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml' // JUnit reporting for unit tests
                jacoco(execPattern: 'target/**/*.exec', classPattern: '**/target/classes', sourcePattern: '**/src/main/java') // Configure JaCoCo report generation
            }
        }

        stage("Container") {
            stage('build') {
                steps {
                    sh "docker image build -f Dockerfile -t ${projectName}:${env.BUILD_ID} ." // Build the container image
                }
            }

            stage('tag') {
                steps {
                    parallel {
                        listContainers: {
                            sh "docker container ls -a" // List containers (optional for debugging)
                        }
                        listImages: {
                            sh "docker image ls -a" // List images (optional for debugging)
                        }
                        tagBuildNumb: {
                            sh "docker tag ${projectName}:${env.BUILD_ID} ndadmin888/${projectName}:${env.BUILD_ID}" // Tag image with build number
                        }
                        tagLatest: {
                            sh "docker tag ${projectName}:${env.BUILD_ID} ndadmin888/${projectName}:latest" // Tag image with "latest"
                        }
                    }
                }
            }

            stage('publish') {
                steps {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_REGISTRY_PWD', usernameVariable: 'DOCKER_REGISTRY_USER')]) {
                        sh """
                            #!/bin/bash
                            docker login -u $DOCKER_REGISTRY_USER -p $DOCKER_REGISTRY_PWD
                            echo 'Login success...'

                            docker push ndadmin888/${projectName}:${env.BUILD_ID}
                            docker push ndadmin888/${projectName}:latest

                            docker logout
                            echo 'Logout...'
                        """ // Login, push images, and logout
                    }
                }
            }

            stage('clean') {
                steps {
                    sh """
                        #!/bin/bash
                        docker images ls
                        echo 'Deleting local images...'

                        docker rmi -f \$(docker images -aq)

                        docker images ls
                    """ // List, delete, and confirm deletion of local images (optional)
                }
            }
        }
    }
}

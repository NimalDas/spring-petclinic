pipeline {
    agent any
    environment {
        dockerRegistryOrg="ndadmin888"
        MAVEN_HOME = tool 'maven'
        CHECKSTYLE_REPORT = 'checkstyle-result.xml'
        JACOCO_REPORT = 'target/site/jacoco/jacoco.xml'
        softwareVersion()
    }
    stages {
        stage('Clean') {
            sh "rm -rf spring-petclinic"
        }
        stage('Clone Github repository') {
            git branch: 'main', url: 'https://github.com/NimalDas/spring-petclinic.git'
        } 
        stage('Compile') {
            sh "${MAVEN_HOME}/bin/mvn clean package -DskipTests=true"
        } 
        stage('Checkstyle') {
            sh "${MAVEN_HOME}/bin/mvn checkstyle:checkstyle"
            junit allowEmptyResults: true, testResults: '**/${CHECKSTYLE_REPORT}'
        } 
        stage('Checkstyle') {
            sh "${MAVEN_HOME}/bin/mvn test jacoco:report"
            junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml'
            jacoco(execPattern: 'target/**/*.exec', classPattern: '**/target/classes', sourcePattern: '**/src/main/java')
        } 
        stage ("Container") {
            stage('build') {
                sh "docker image build -f Dockerfile -t ${projectName}:${env.BUILD_ID} ."
            } 
            stage('tag') {
                parallel listContainers: {
                    sh "docker container ls -a"                    
                }, listImages: {
                    sh "docker image ls -a"
                }, tagBuildNumb: {
                        sh "docker tag ${projectName}:${env.BUILD_ID} ndadmin888/${projectName}:${env.BUILD_ID}"
                }, tagLatest: {
                    sh "docker tag ${projectName}:${env.BUILD_ID} ndadmin888/${projectName}:latest"
                }
            } 
            stage('publish') {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_REGISTRY_PWD', usernameVariable: 'DOCKER_REGISTRY_USER')]) {
                    sh """ #!/bin/bash
                        docker login -u $DOCKER_REGISTRY_USER -p $DOCKER_REGISTRY_PWD
                        echo 'Login success...'

                        docker push ndadmin888/${projectName}:${env.BUILD_ID}
                        docker push ndadmin888/${projectName}:latest

                        docker logout
                        echo 'Logut...'
                    """
                } 
            } 
            stage('clean') {
                sh """ #!/bin/bash 
                    docker images ls 
                    echo 'Deleting local images...' 

                    docker rmi -f \$(docker images -aq)

                    docker images ls 
                """ 
            } 
        } 
    }
def softwareVersion() {
    sh """ #!/bin/bash
        java -version
        mvn -version
        docker version
        echo '\n'
    """
}
} 
    
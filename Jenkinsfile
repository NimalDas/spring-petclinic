pipeline {
    agent any
    
    environment {
        PROJECT_NAME = 'petclinic'
        DOCKER_ID = 'ndadmin888'
        MAVEN_HOME = tool 'maven'
        IMAGE_NAME = 'spring-petclinic'
        DOCKER_REGISTRY = 'http:localhost:8082/artifactory'
        ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-token')
        CHECKSTYLE_REPORT = 'checkstyle-result.xml'
        JACOCO_REPORT = 'target/site/jacoco/jacoco.xml'
        SONAR_URL = 'http:localhost:9000'
        SNYK_TOKEN = credentials('snyk-token')
        SNYK_ORG_ID = 'b634403f-1c7a-43d3-9043-7269a4ab2e32'
    }

    stages {
        stage('Clone Git repository') {
            steps {
                git branch: 'main', url: 'https://github.com/NimalDas/spring-petclinic.git'
            }
        }
        stage('Clean') {
            steps {
                sh 'rm -rf spring-petclinic'
            }   
        }
        stage('Checkstyle') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn checkstyle:checkstyle"
                junit allowEmptyResults: true, testResults: '**/${CHECKSTYLE_REPORT}'
            }
        }

       //stage('SAST Scan with Snyk Code') {
       //    steps {
       //        script {
       //            sh 'snyk auth ${SNYK_TOKEN}'
       //            sh 'snyk code test --org ${SNYK_ORG_ID} --project ${PROJECT_NAME} --report ${PROJECT_NAME} --fail-on-critical'  
       //        }
       //    }
       //}

        stage('Compile') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn clean package -DskipTests=true"
            }
        }
              
    //  stage('Unit Tests & Code Coverage') {
    //      steps {
    //          sh "${MAVEN_HOME}/bin/mvn test jacoco:report"
    //          junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml'
    //          jacoco(execPattern: 'target/**/*.exec', classPattern: '**/target/classes', sourcePattern: '**/src/main/java')
    //      }
    //  }
        stage('Code Coverage') {
            steps {
                step([$class: 'JacocoPublisher']) // JaCoCo report generation
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv('sonarqube-server') {
                        sh "${MAVEN_HOME}/bin/mvn sonar:sonar -Dsonar.host.url=${SONAR_URL} -Dsonar.login=${SONAR_TOKEN}"
                    }
                }
            }
        }
    
        stage('Docker Build') {
            steps{
                sh "docker build -t ${IMAGE_NAME} ."
                sh "docker tag ${IMAGE_NAME} ${DOCKER_ID}/${IMAGE_NAME}:latest"  
            }
        }
        stage('Upload to Dockerhub') {
            steps{
                script{
                   withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker') {
                        sh "docker push ${DOCKER_ID}/${IMAGE_NAME}:latest"
                    }
                }
            }
        }

        stage('Upload to maven artifacts to JF Artifactory') {
            steps {
                sh 'jf rt upload --url http://localhost:8082/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/*.jar petclinic/'
            }
        }

        stage('Upload docker image to JF Artifactory') {
            steps {
                script {
                withDockerRegistry(credentialsId: 'jfrog-creds', toolName: 'docker', url: '${DOCKER_REGISTRY}' ) {  // Add the JFrog Artifactory URL here
                    jf 'docker scan $DOCKER_IMAGE_NAME'
                    jf 'docker push http://localhost:8082/artifactory/${IMAGE_NAME}:latest'
                }
                }
            }
        }
    }
}

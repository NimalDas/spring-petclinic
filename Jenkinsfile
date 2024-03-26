pipeline {
    agent any
    
    environment {
        MAVEN_HOME = tool 'maven'
        DOCKER_REGISTRY = 'http:localhost:8082/artifactory'
        DOCKER_IMAGE = 'spring-petclinic'
        ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-token')
        CHECKSTYLE_REPORT = 'checkstyle-result.xml'
        JACOCO_REPORT = 'target/site/jacoco/jacoco.xml'
        SONAR_URL = 'http:localhost:9000' // Adjust URL according to your SonarQube instance
    }

    stages {
        stage('Clone Git repository') {
            steps {
                git branch: 'main', url: 'https://github.com/NimalDas/spring-petclinic.git'
            }
        }

        stage('Compile') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn clean package -DskipTests=true"
            }
        }
        stage('Checkstyle') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn checkstyle:checkstyle"
                junit allowEmptyResults: true, testResults: '**/${CHECKSTYLE_REPORT}'
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
                sh "docker build -t petclinic ."
                sh "docker tag petclinic ndadmin888/pet-clinic:latest"  
            }
        }
        stage('Docker Push') {
            steps{
                script{
                   withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker') {
                        sh "docker push ndadmin888/pet-clinic:latest"
                    }
                }
            }
        }

        // Upload to artifactory
    /*stage('Build Artifacts') {
      steps {
        // Ensure successful build before uploading artifacts
        script {
          if (currentBuild.result == 'SUCCESS') {
            echo 'Build successful, proceeding with artifact upload...'
          } else {
            error 'Build failed, skipping artifact upload.'
          }
        }
      }
    }
    */
    // New stage to upload binaries to JFrog Artifactory
        stage('Upload to Artifactory') {
            agent {
                docker {
                image 'releases-docker.jfrog.io/jfrog/jfrog-cli-v2:2.2.0' 
                reuseNode true
                }
            }
            steps {
                sh 'jfrog rt upload --url http://localhost:8082/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/*.jar petclinic/'
            }
            }

    }
}

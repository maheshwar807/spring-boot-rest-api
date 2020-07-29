pipeline {
    agent any
    tools {
        maven "Maven"
    }
    stages {
        stage("Clone code from VCS") {
            steps {
                script {
                    git 'https://github.com/maheshwar807/spring-boot-rest-api.git';
                }
            }
        }
        stage("Maven Install") {
            steps {
                script {
                    withSonarQubeEnv('SonarQube') {
                        sh "mvn clean install sonar:sonar"
                    }
                }
            }
        }
        stage("Quality Gate"){
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                    sh "mvn deploy"
                }
            }
        }
    }
}

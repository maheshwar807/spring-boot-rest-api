@Library('jenkins-shared-library@master') _

pipeline {
   agent any
   tools {
       maven "Maven"
   }
   environment {
       NEXUS_VERSION = "nexus3"
       NEXUS_PROTOCOL = "http"
       NEXUS_URL = "http://35.225.210.223:8081/repository/maven-snapshots/"
       NEXUS_REPOSITORY = "maven-snapshots"
       NEXUS_CREDENTIAL_ID = "nexus-user-credentials"
   }
   stages {
       stage("Maven Build") {
           steps {
               script {
                   withSonarQubeEnv('SonarQube') {
                       sh "mvn clean install sonar:sonar -s settings.xml"
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
                   pom = readMavenPom file: "pom.xml";
                   filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                   echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                   artifactPath = filesByGlob[0].path;
                   artifactExists = fileExists artifactPath;
                   if(artifactExists) {
                       publishToNexus(artifactPath, NEXUS_URL)
                   } else {
                       error "*** File: ${artifactPath}, could not be found";
                   }
               }
           }
       }
   }
}

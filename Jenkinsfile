pipeline {
    agent any 
     tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "localmaven"
        git "Default"
    }
    stages {
        stage('SCM Checkout') {
            steps{
                git branch: 'master', credentialsId: 'git-token', url: 'https://github.com/harshbaghel52/chatapps.git'
            }
        }
        stage('MVN Build') {
          steps{
             sh "mvn -Dmaven.test.failure.ignore=true clean package"
             sh "mv target/websocket-demo-0.0.1-SNAPSHOT.jar target/chatapp.jar"
          }
       }
       stage('MVN Testing') {
          steps{
             sh "mvn test"
          }
       }
       stage('test report') {
          steps{
             junit 'target/surefire-reports/*.xml'
          }
          post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
       }
       stage('Get ansible code') {
           when {
                expression {choice == '1'}
            }
          
          steps{
             
                git "https://github.com/harshbaghel52/exercise2.git"
          }
       }
       stage('execute ansible') {
           when {
                expression {choice == '2'}
            }
            
          steps{
             
                sshagent(['ansible-2']) {
                 ansiblePlaybook inventory:  'dev.inv',disableHostKeyChecking: true,  playbook: 'tomcat.yml'
              }

          }
       }
       stage('deploy jar in tomcat') {
            
          steps{
            sshagent(['ansible-2']) {
              sh "scp -r -o StrictHostKeyChecking=no target/chatapp.jar root@tomcat:/usr/local/"
              }

          }
       }
    }
}

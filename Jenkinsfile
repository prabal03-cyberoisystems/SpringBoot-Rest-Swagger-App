pipeline {
    agent {
        node {
              label 'jenkins-node-1'
          }
     }
    tools {
        maven 'Maven' // specify the tool identifier
    }
    
    stages {
        stage('Build') {                           // Build stage 
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {                            // Test stage 
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Deliver') {
            steps {
                sh '''
                   sudo su 
                   id
                   cp -r target/SpringBootApp target/SpringBootApp.war /var/lib/docker/volumes/jenkins-tomcat/_data
                   '''
                 
            }
        }
    }
}



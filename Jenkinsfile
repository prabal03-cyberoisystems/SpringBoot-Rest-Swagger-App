pipeline {
    agent {
        node {
              label 'jenkins-node-1'
          }
     }
    tools {
        maven 'Maven' // specify the tool identifier
        snyk  'Snyk' // specify the tool identifier
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
        stage('Security Scan') {                    // snyk stage
            steps {
               catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE', allowEmptyResults: true) {
               withCredentials([string(credentialsId: 'SNYK_API_TOKEN', variable: 'SNYK_API_KEY')]) {
               sh '''
                  export SNYK_TOKEN=$SNYK_API_KEY
                  /usr/bin/snyk test
                  /usr/bin/snyk monitor
                  '''
             }
          }
       }
     }

        stage('Deliver') {
            steps {
                sh 'sudo cp -r target/SpringBootApp target/SpringBootApp.war /var/lib/docker/volumes/jenkins-tomcat/_data'
                 
            }
        }
    }
}



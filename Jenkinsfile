pipeline {
    agent {
        node {
              label 'jenkins-node-1'               // Node In which jenkins will deploy
          }
     }
    tools {
        maven 'Maven' // specify the tool identifier  // Declarative tools will install at run time
        snyk  'Snyk' // specify the tool identifier
    } 
    
    stages {
        stage('SonarQube analysis') {    
         steps {         
          script {
          // requires SonarQube Scanner 2.8+
          scannerHome = tool 'SonarQube'
        }
             withSonarQubeEnv('SonarQube') {     
                     sh "mvn sonar:sonar -Dsonar.projectKey=bridget-sonarqube -Dsonar.projectName='bridget'"
           }
         }
       }
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
               catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
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

        stage('Deliver') {                           // Delivery Stage
            steps {
                sh 'sudo cp -r target/SpringBootApp target/SpringBootApp.war /var/lib/docker/volumes/jenkins-tomcat/_data'
                 
            }
        }
    }
}

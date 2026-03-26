pipeline {
    agent any

    tools {
    maven 'Maven'   // or whatever name you see in Manage Jenkins → Tools → Maven
    jdk 'java'      // or whatever name you see for JDK
         }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-credentials', // your GitHub credentials ID
                    url: 'https://github.com/Scroll-7/devops-webapp.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.war', fingerprint: true
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                // This assumes Tomcat is on localhost:8081 and Jenkins can copy the WAR
                sh '''
                    sudo cp target/*.war /opt/tomcat/webapps/devops-demo.war
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build and deploy successful!'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}

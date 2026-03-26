pipeline {
    agent any

    tools {
        maven 'Maven'          // Name of Maven installation in Jenkins
        jdk 'java'             // Name of JDK installation in Jenkins
    }

    environment {
        // Tomcat credentials stored in Jenkins (ID 'tomcat-credentials')
        TOMCAT_CREDS = credentials('tomcat-credentials')
        TOMCAT_URL = 'http://localhost:8081/manager/text'
        APP_PATH = '/devops-demo'
        WAR_FILE = 'target/*.war'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-credentials',
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
                script {
                    // Find the exact WAR file name (there should be only one)
                    def warFile = findFiles(glob: 'target/*.war')[0].path
                    
                    // Deploy using Tomcat Manager API
                    sh """
                        curl -u ${TOMCAT_CREDS_USR}:${TOMCAT_CREDS_PSW} \
                             -T ${warFile} \
                             "${TOMCAT_URL}/deploy?path=${APP_PATH}&update=true"
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo '✅ Build and deployment successful!'
        }
        failure {
            echo '❌ Build or deployment failed.'
        }
    }
}

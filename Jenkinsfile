pipeline {
    agent any
    tools {
        // These names must match Manage Jenkins -> Tools EXACTLY
        maven 'Maven_3.9' 
        nodejs 'Node_20'
    }
    environment {
        TEAMS_WEBHOOK = credentials('jenkins-teams-webhook')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Backend (Spring Boot)') {
            steps {
                dir('backend') {
                    // This will catch your real Java/Spring errors
                    bat 'mvn clean install -DskipTests'
                }
            }
        }
        stage('Build Frontend (Angular)') {
            steps {
                dir('frontend') {
                    // This will catch your real TypeScript/Angular errors
                    bat '''
                        npm install
                        npm run build
                    '''
                }
            }
        }
    }
    post {
        failure {
            script {
                sendTeamsNotification("FAILURE", "Attention", "❌")
            }
        }
        success {
            script {
                sendTeamsNotification("SUCCESS", "Good", "✅")
            }
        }
    }
}

def sendTeamsNotification(String status, String color, String icon) {
    bat """
    curl -k --ssl-no-revoke -f -H "Content-Type: application/json" -d "{\\"type\\": \\"AdaptiveCard\\", \\"body\\": [{\\"type\\": \\"TextBlock\\", \\"text\\": \\"${icon} Jenkins Build ${status}\\", \\"weight\\": \\"Bolder\\", \\"size\\": \\"Large\\", \\"color\\": \\"${color}\\"}, {\\"type\\": \\"TextBlock\\", \\"text\\": \\"Project: ${env.JOB_NAME}\\", \\"wrap\\": true}, {\\"type\\": \\"TextBlock\\", \\"text\\": \\"Build: #${env.BUILD_NUMBER}\\", \\"wrap\\": true}], \\"actions\\": [{\\"type\\": \\"Action.OpenUrl\\", \\"title\\": \\"View Build\\", \\"url\\": \\"${env.BUILD_URL}\\"}], \\"\$schema\\": \\"http://adaptivecards.io/schemas/adaptive-card.json\\", \\"version\\": \\"1.2\\"}" "%TEAMS_WEBHOOK%"
    """
}
pipeline {
    agent any
    environment {
        // Ensure this ID matches what you saved in Jenkins Credentials
        TEAMS_WEBHOOK = credentials('jenkins-teams-webhook')
    }
    stages {
        stage('Build Backend') {
            steps {
                dir('backend') {
                    // Using 'bat' because you are on Windows
                    bat 'echo Building Backend... && exit 0' 
                }
            }
        }
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    bat 'echo Building Frontend... && exit 0'
                }
            }
        }
        stage('Simulate Failure') {
            steps {
                // This forces the failure so we can test the notification
                bat 'echo Inducing Failure... && exit 1'
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
    // Windows requires double quotes for JSON and escaped double quotes for internal strings
    // We use %TEAMS_WEBHOOK% because 'bat' uses Windows CMD syntax
    bat """
    curl -f -H "Content-Type: application/json" -d "{\\"type\\": \\"AdaptiveCard\\", \\"body\\": [{\\"type\\": \\"TextBlock\\", \\"text\\": \\"${icon} Jenkins Build ${status}\\", \\"weight\\": \\"Bolder\\", \\"size\\": \\"Large\\", \\"color\\": \\"${color}\\"}, {\\"type\\": \\"TextBlock\\", \\"text\\": \\"Project: ${env.JOB_NAME}\\", \\"wrap\\": true}, {\\"type\\": \\"TextBlock\\", \\"text\\": \\"Build: #${env.BUILD_NUMBER}\\", \\"wrap\\": true}], \\"actions\\": [{\\"type\\": \\"Action.OpenUrl\\", \\"title\\": \\"View Build\\", \\"url\\": \\"${env.BUILD_URL}\\"}], \\"\$schema\\": \\"http://adaptivecards.io/schemas/adaptive-card.json\\", \\"version\\": \\"1.2\\"}" %TEAMS_WEBHOOK%
    """
}
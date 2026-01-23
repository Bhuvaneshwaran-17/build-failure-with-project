pipeline {
    agent any
    environment {
        // 'jenkins-teams-webhook' must match the ID you gave in Jenkins Credentials
        TEAMS_WEBHOOK = credentials('jenkins-teams-webhook')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                // Change this to your actual build command (mvn, npm, etc.)
                sh 'echo "Building project..." && exit 0' 
            }
        }
    }
    post {
        success {
            script {
                sendTeamsNotification("SUCCESS", "Good", "✅")
            }
        }
        failure {
            script {
                sendTeamsNotification("FAILURE", "Attention", "❌")
            }
        }
    }
}

// Helper function to keep the pipeline clean
def sendTeamsNotification(String status, String color, String icon) {
    sh """
    curl -f -H 'Content-Type: application/json' -d '{
        "type": "AdaptiveCard",
        "body": [
            { "type": "TextBlock", "text": "${icon} Jenkins Build ${status}", "weight": "Bolder", "size": "Large", "color": "${color}" },
            { "type": "TextBlock", "text": "Project: ${env.JOB_NAME}", "wrap": true },
            { "type": "TextBlock", "text": "Build: #${env.BUILD_NUMBER}", "wrap": true }
        ],
        "actions": [
            { "type": "Action.OpenUrl", "title": "View Build", "url": "${env.BUILD_URL}" }
        ],
        "\$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
        "version": "1.2"
    }' \${TEAMS_WEBHOOK}
    """
}

pipeline {
    agent any
    
    stages {
        stage('Run Pipeline') {
            steps {
                script {
                    sh 'chmod +x ./CICD'
                    sh './CICD'
                }
            }
        }
    }
    
    post {
        success {
            echo "Pipeline successfully triggered CICD job"
        }
        failure {
            echo "Failed to trigger CICD job"
        }
    }
}
pipeline {
    agent any
    
    stages {
        stage('Run Pipeline') {
            steps {
                script {
                    build job: 'CICD'
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
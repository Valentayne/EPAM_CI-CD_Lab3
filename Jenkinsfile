pipeline {
    agent any

    stages {
        stage('Trigger CICD Job') {
            steps {
                build job: 'CICD', propagate: true
            }
        }
    }
}

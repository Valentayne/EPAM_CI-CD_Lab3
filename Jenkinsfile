pipeline {
    agent any

    stages {
        stage('Trigger CICD Job') {
            steps {
                build job: 'CI CD', propagate: true
            }
        }
    }
}

pipeline {
    agent {
        docker {
            image 'node:7.8.0'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        IMAGE_TAG = "v1.0"
    }

    stages {
        stage('Set ENV') {
            steps {
                script {
                    env.APP_PORT = (env.BRANCH_NAME == 'main') ? '3000' : '3001'
                    env.IMAGE_NAME = (env.BRANCH_NAME == 'main') ? 'nodemain' : 'nodedev'
                    env.CONTAINER_NAME = (env.BRANCH_NAME == 'main') ? 'nodemain-app' : 'nodedev-app'
                }
            }
        }

        stage('Build Application') {
            steps {
                sh 'chmod +x ./scripts/build.sh'
                sh './scripts/build.sh'
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'chmod +x ./scripts/test.sh'
                sh './scripts/test.sh'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${env.IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh "docker stop ${env.CONTAINER_NAME} || true"
                    sh "docker rm ${env.CONTAINER_NAME} || true"

                    sh """
                        docker run -d \
                          --name ${env.CONTAINER_NAME} \
                          -p ${env.APP_PORT}:${env.APP_PORT} \
                          --restart unless-stopped \
                          ${env.IMAGE_NAME}:${IMAGE_TAG}
                    """

                    sh "docker ps | grep ${env.CONTAINER_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo "Deploy complete! App is ready on port: ${env.APP_PORT}"
            echo "Image: ${env.IMAGE_NAME}:${IMAGE_TAG}"
        }
        failure {
            echo "Pipeline finished with error"
        }
        always {
            sh "docker rm -f ${env.CONTAINER_NAME}-temp || true"
        }
    }
}
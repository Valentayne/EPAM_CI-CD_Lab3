pipeline {

    agent any

    tools {
        nodejs 'node'
    }

    environment {
        IMAGE_TAG = "v1.0"
    }

    stages {

        stage('Set ENV') {
            steps {
                script {
                    APP_PORT = (env.BRANCH_NAME == 'main') ? '3000' : '3001'
                    IMAGE_NAME = (env.BRANCH_NAME == 'main') ? 'nodemain' : 'nodedev'
                    CONTAINER_NAME = (env.BRANCH_NAME == 'main') ? 'nodemain-app' : 'nodedev-app'
                }
            }
        }

        stage('Checkout') {
            steps {
                git 'https://github.com/Valentayne/EPAM_CI-CD_Lab3.git'
            }
        }

        stage('Build Application') {
            steps {
                sh 'chmod +x ./public/scripts/build.sh'
                sh './public/scripts/build.sh'
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'chmod +x ./public/scripts/test.sh'
                sh './public/scripts/test.sh'
            }
        }    
        
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"

                    sh """
                        docker run -d \
                          --name ${CONTAINER_NAME} \
                          --expose ${APP_PORT} \
                          -p ${APP_PORT}:${APP_PORT} \
                          --restart unless-stopped \
                          ${IMAGE_NAME}:${IMAGE_TAG}
                    """

                    sh "docker ps | grep ${CONTAINER_NAME}"
                    sh "docker logs --tail=50 ${CONTAINER_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo "Deploy complete! App is ready on port: ${APP_PORT}"
            echo "Image: ${IMAGE_NAME}:${IMAGE_TAG}"
        }
        failure {
            echo "Pipeline finished with error"
        }
        always {
            sh "docker rm -f ${CONTAINER_NAME}-temp || true"
        }
    }
}

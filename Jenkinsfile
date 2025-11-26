pipeline {

    agent any

    tools {
        nodejs 'node'
    }

    environment {
        IMAGE_TAG = "v1.0"
        ENV_SUFFIX = "${env.BRANCH_NAME == 'main' ? 'main' : 'dev'}"
        APP_PORT = "${ENV_SUFFIX == 'main' ? '3000' : '3001'}"
        IMAGE_NAME = "node${ENV_SUFFIX}"
        CONTAINER_NAME = "node${ENV_SUFFIX}-app"
    }

    stages {

        stage('Verify Docker') {
            steps {
                sh 'docker --version'
                sh 'docker ps'
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
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy') {
            steps {
                sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${APP_PORT}:3000 \
                        --restart unless-stopped \
                        ${IMAGE_NAME}:${IMAGE_TAG}

                    docker ps | grep ${CONTAINER_NAME}
                    docker logs --tail=50 ${CONTAINER_NAME}
                """
            }
        }
    }

    post {
        success {
            echo "Deploy complete! App is ready on port: ${APP_PORT}"
            echo "Image: ${IMAGE_NAME}:${IMAGE_TAG}"
            echo "Container: ${CONTAINER_NAME}"
        }
        failure {
            echo "Pipeline finished with error"
        }
        always {
            sh "docker rm -f ${CONTAINER_NAME}-temp || true"
        }
    }
}

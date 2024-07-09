pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "your-docker-image-name"
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from your repository
                git branch: 'main', url: 'https://github.com/sialorama/cicdjenkins.git', credentialsId: 'gitcred'
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build the Docker image
                    try {
                        sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error "Failed to build Docker image: ${e.message}"
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run tests within a Docker container created from the built image
                    try {
                        sh "docker run --rm ${DOCKER_IMAGE}:${DOCKER_TAG} ./mvn"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error "Tests failed: ${e.message}"
                    }
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    // Use Docker credentials to log in and push the image
                    try {
                        withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials-id', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                            sh "echo \$DOCKER_HUB_PASSWORD | docker login -u \$DOCKER_HUB_USERNAME --password-stdin"
                            sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error "Failed to push Docker image: ${e.message}"
                    }
                }
            }
        }
    }

    post {
        always {
            // Cleanup
            script {
                try {
                    sh "docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG}"
                } catch (Exception e) {
                    echo "Failed to clean up Docker image: ${e.message}"
                }
            }
        }
    }
}

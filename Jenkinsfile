pipeline {
    agent {
        docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock' // Mount Docker socket
        }
    }

    environment {
        // Define environment variables for Docker image name and tag
        DOCKER_IMAGE = "your-docker-image-name"
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the specified GitHub repository
                git branch: 'main', url: 'https://github.com/sialorama/cicdjenkins.git', credentialsId: 'gitcred'
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build the Docker image using the Dockerfile in the repository
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run tests in a Docker container created from the built image
                    // Replace 'your-test-command' with the actual command to run your tests
                    sh "docker run --rm ${DOCKER_IMAGE}:${DOCKER_TAG} your-test-command"
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    // Use Docker credentials to log in and push the image to Docker Hub
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials-id', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                        // Log in to Docker Hub
                        sh "echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin"
                        // Push the Docker image to Docker Hub
                        sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }
    }

    post {
        always {
            // Cleanup step to remove the Docker image from the local registry
            script {
                sh "docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }
}

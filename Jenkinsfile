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
                git branch: 'main', url: 'https://github.com/sialorama/cicdjenkins.git', credentialsId: 'credgit'
            }
        }

        stage('Build Jar') {
            steps {
                script {
                    // Assuming you have a build script or a command to create cicdjenkins.jar
                    sh './gradlew build' // Adjust this command to match your build tool
                }
                archiveArtifacts artifacts: '**/build/libs/*.jar', fingerprint: true // Adjust the path to your JAR
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image, including the cicdjenkins.jar in the Dockerfile context
                    sh """
                        cp build/libs/cicdjenkins.jar .
                        docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                    """
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
                        sh "echo \$DOCKER_HUB_PASSWORD | docker login -u \$DOCKER_HUB_USERNAME --password-stdin"
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
                def dockerImage = env.DOCKER_IMAGE
                def dockerTag = env.DOCKER_TAG
                sh "docker rmi ${dockerImage}:${dockerTag}"
            }
        }
    }
}

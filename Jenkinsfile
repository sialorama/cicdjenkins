pipeline {
    agent any

    environment {
        // Define environment variables if needed
        DOCKER_IMAGE = "your-docker-image-name"
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Checkout') {
             steps {
                 // Checkout the code from the repository
                 git branch: 'main', url:'https://github.com/sialorama/cicdjenkins.git', credentialsId: 'credgit'
             }
        }

        stage('Build') {
            steps {
                script {
                    // Build the Docker image
                    sh "./mvn clean "
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run your tests here
                    // For example, you could run a container from the built image and execute tests inside it
                    sh "docker run --rm ${DOCKER_IMAGE}:${DOCKER_TAG} bash ./mvn test"
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    // Use Docker credentials to log in and push the image
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials-id', usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                        sh "echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin"
                        sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }
    }

    post {
        always {
            // Cleanup
            script {
                sh "docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }
}
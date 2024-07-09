pipeline {
    agent any

stage('Checkout') {
            steps {
                // Checkout the code from the repository
                git branch: 'main', url:'https://github.com/sialorama/cicdjenkins.git', credentialsId: 'credgit'

            }
        }

        stage('Build') {
            steps {
                // Build the Spring Boot application
                sh './mvnw clean install'
            }
        }

        stage('Test') {
            steps {
                // Run the tests
                sh './mvnw test'
            }
        }

        stage('Package') {
            steps {
                // Package the application
                sh './mvnw package'
            }
        }

    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
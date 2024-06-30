pipeline {
    agent {
        docker {
            image 'maven:3.8.1-jdk-11'  // Maven image with JDK 11
            args '-v /root/.m2:/root/.m2'  // To cache Maven dependencies
        }
    }
    environment {
        // Define environment variables if needed
        DOCKER_IMAGE = "myapp:latest"
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout source code from your SCM (e.g., Git)
                git 'https://github.com/your-repo/your-project.git'
            }
        }
        stage('Build') {
            steps {
                // Compile and build your Maven project
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                // Run your tests
                sh 'mvn test'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // Login to Docker Hub (assumes credentials are set in Jenkins)
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                        // Push Docker image to Docker Hub
                        sh 'docker push $DOCKER_IMAGE'
                    }
                }
            }
        }
    }
    post {
        always {
            // Clean up the workspace and Docker images to save space
            cleanWs()
            sh 'docker rmi $DOCKER_IMAGE || true'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

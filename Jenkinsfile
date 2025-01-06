pipeline {
    agent any

    environment {
        IMAGE_NAME = 'testimage'  // Set image name to "testimage"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Building the application...'
                    sh './scripts/build.sh'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    echo 'Running tests...'
                    sh './scripts/test.sh'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    sh 'docker build -t ${IMAGE_NAME}:latest .'  // Tag the image as "latest"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Pushing Docker image to registry...'
                    docker.withRegistry('https://registry.hub.docker.com', 'bf273b9f-a7af-482c-981d-24d87ad00d25') {
                        // Push the image with the "latest" tag
                        sh 'docker push ${IMAGE_NAME}:latest'
                    }
                }
            }
        }
    }
}

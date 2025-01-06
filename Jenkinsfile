pipeline {
    agent any
    
    environment {
        IMAGE_NAME = 'testimage'
        DOCKER_REGISTRY = 'registry.hub.docker.com'
        DOCKER_CREDENTIALS = 'bf273b9f-a7af-482c-981d-24d87ad00d25'
    }
    
    stages {
        stage('Verify Workspace and Build Script') {
            steps {
                script {
                    echo 'Checking the workspace and build script...'
                    sh 'pwd'
                    sh 'ls -l ./scripts'
                }
            }
        }
        
        stage('Verify Jenkins User') {
            steps {
                script {
                    echo 'Checking the Jenkins user...'
                    sh 'whoami'
                    sh 'ls -l'
                }
            }
        }
        
        stage('Build') {
            steps {
                script {
                    echo 'Building the application...'
                    sh 'chmod +x ./scripts/build.sh'
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
                    docker.build("${env.IMAGE_NAME}:${env.BUILD_NUMBER}", '--platform linux/arm64 .')
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Pushing Docker image to registry...'
                    docker.withRegistry("https://${env.DOCKER_REGISTRY}", env.DOCKER_CREDENTIALS) {
                        def dockerImage = docker.image("${env.IMAGE_NAME}:${env.BUILD_NUMBER}")
                        
                        // Push the build-numbered tag
                        dockerImage.push()
                        
                        // Push the latest tag
                        dockerImage.push('latest')
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed! Please check the logs for details.'
        }
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
    }
}

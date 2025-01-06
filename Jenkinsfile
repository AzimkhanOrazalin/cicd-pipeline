pipeline {
    agent any
    
    environment {
        IMAGE_NAME = 'testimage'
        DOCKER_REGISTRY = 'registry.hub.docker.com'
        DOCKER_CREDENTIALS_ID = 'bf273b9f-a7af-482c-981d-24d87ad00d25'
        BUILD_TAG = "${env.BUILD_NUMBER}"
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
                    sh "docker build --platform linux/arm64 -t ${DOCKER_USERNAME}/${env.IMAGE_NAME}:${env.BUILD_TAG} ."
                    sh "docker tag ${DOCKER_USERNAME}/${env.IMAGE_NAME}:${env.BUILD_TAG} ${DOCKER_USERNAME}/${env.IMAGE_NAME}:latest"
                }
            }
        }
    
        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Pushing Docker image to registry...'
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, 
                                                    usernameVariable: 'DOCKER_USER', 
                                                    passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                        
                        // Push both tags
                        sh "docker push ${DOCKER_USERNAME}/${env.IMAGE_NAME}:${env.BUILD_TAG}"
                        sh "docker push ${DOCKER_USERNAME}/${env.IMAGE_NAME}:latest"
                        
                        sh 'docker logout'
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

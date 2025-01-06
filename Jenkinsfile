pipeline {
  agent any
  stages {
    stage('Verify Workspace and Build Script') {
      steps {
        script {
          echo 'Checking the workspace and build script...'
          sh 'pwd'  // Print the working directory
          sh 'ls -l ./scripts'  // List the files in the "scripts" directory
        }

      }
    }

    stage('Verify Jenkins User') {
      steps {
        script {
          echo 'Checking the Jenkins user...'
          sh 'whoami'  // Print the user Jenkins is running as
          sh 'ls -l'   // List the files in the current directory to check file access
        }

      }
    }

    stage('Build') {
      steps {
        script {
          echo 'Building the application...'
          // Make sure the build script has execute permissions
          sh 'chmod +x ./scripts/build.sh'
          sh './scripts/build.sh'  // Execute the build script
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
          sh 'docker build --platform linux/arm64 -t ${IMAGE_NAME}:latest .'
          def app = docker.image("${IMAGE_NAME}:latest")
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
            
            def app = docker.image("${IMAGE_NAME}:latest")
            app.push("${env.BUILD_NUMBER}")
            app.push('latest')
          }
        }

      }
    }

  }
  environment {
    IMAGE_NAME = 'testimage'
  }
}

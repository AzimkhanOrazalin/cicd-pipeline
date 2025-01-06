pipeline {
  agent any
  stages {
    stage('script build') {
      steps {
        sh '''pipeline {
    agent any
    stages {
        stage(\'Build\') {
            steps {
                script {
                    echo \'Building the application...\'
                    sh \'./scripts/build.sh\'
                }
            }
        }
    }
}'''
        }
      }

      stage('test') {
        steps {
          sh '''pipeline {
    agent any
    stages {
        stage(\'Test\') {
            steps {
                script {
                    echo \'Running tests...\'
                    sh \'./scripts/test.sh\'
                }
            }
        }
    }
}
'''
          }
        }

        stage('build image') {
          steps {
            sh '''pipeline {
    agent any
    stages {
        stage(\'Build Docker Image\') {
            steps {
                script {
                    echo \'Building Docker image...\'
                    sh \'docker build -t mybuildimage .\'
                }
            }
        }
    }
}
'''
            }
          }

          stage('push') {
            steps {
              sh '''pipeline {
    agent any
    stages {
        stage(\'Push Docker Image\') {
            steps {
                script {
                    echo \'Pushing Docker image to registry...\'
                    docker.withRegistry(\'https://registry.hub.docker.com\', \'docker_hub_creds_id\') {
                        sh \'docker push mybuildimage:latest\'
                    }
                }
            }
        }
    }
}
'''
              }
            }

          }
        }
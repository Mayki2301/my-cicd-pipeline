pipeline {
    agent any

    tools {
        nodejs 'NodeJS-7.8.0'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker build -t nodemain:v1.0 .'
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker build -t nodedev:v1.0 .'
                    } else {
                        error "Unsupported branch: ${env.BRANCH_NAME}"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def imageName = env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    def port = env.BRANCH_NAME == 'main' ? '3000' : '3001'
                    def containerName = "${env.BRANCH_NAME}_app"

                    sh """
                        docker ps -a --filter "name=${containerName}" -q | xargs -r docker stop
                        docker ps -a --filter "name=${containerName}" -q | xargs -r docker rm
                        docker run -d --name ${containerName} -p ${port}:3000 ${imageName}
                    """
                }
            }
        }
    }
}

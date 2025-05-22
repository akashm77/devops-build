pipeline {
    agent any
    environment {
        DOCKER_CREDENTIALS_ID = 'fbe4a5bb-dbdb-4863-a7a6-840f257cf4c1'
        DOCKER_DEV_REPO = 'akashm77/dev'
        DOCKER_PROD_REPO = 'akashm77/prod'
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: "${env.BRANCH_NAME}", credentialsId: 'b185de1d-4c8c-4eb7-b709-4d2fbc3aa52b', url: 'https://github.com/akashm77/devops-build.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def imageTag = "${env.BUILD_NUMBER}"
                    if (env.BRANCH_NAME == 'dev') {
                        docker.build("${DOCKER_DEV_REPO}:${imageTag}")
                    } else if (env.BRANCH_NAME == 'master') {
                        docker.build("${DOCKER_PROD_REPO}:${imageTag}")
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_CREDENTIALS_ID) {
                        def imageTag = "${env.BUILD_NUMBER}"
                        if (env.BRANCH_NAME == 'dev') {
                            docker.image("${DOCKER_DEV_REPO}:${imageTag}").push()
                        } else if (env.BRANCH_NAME == 'master') {
                            docker.image("${DOCKER_PROD_REPO}:${imageTag}").push()
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh './deploy.sh'
            }
        }
    }
}

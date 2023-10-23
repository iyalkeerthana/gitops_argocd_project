pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "iyalkavi"
        APP_NAME = "gitops-argo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }
    stages {
        stage ('CleanUp') {
            steps{
                script {
                    cleanWs()
                }
            }
        }
        stage('Checkout from SCM') {
            steps {
                script {
                    git credentialsId: 'GitHub',
                    url: 'https://github.com/iyalkeerthana/gitops_argocd_project.git',
                    branch: 'main'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // sh 'docker build -t "${IMAGE_NAME}" --file ./Dockerfile '
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push the image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('',REGISTRY_CREDS) {
                        docker_image.push("BUILD_NUMBER")
                        docker_image.push('latest')
                    }


                }
            }
        }
    }
}
// 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
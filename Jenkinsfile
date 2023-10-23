pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "iyalkavi"
        APP_NAME = "gitops-argo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = 'docker hub'
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
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push the image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('',REGISTRY_CREDS) {
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }
        }
        stage('Delete Docker images') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        stage('Updating K8s Deployment file') {
            steps {
                script {
                    sh """
                    cat deployment.yml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                    cat deployment.yml
                    """

                }
            }
        }
    }
}
// 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
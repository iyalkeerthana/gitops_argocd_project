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
        stage('trigger CD Pipeline') {
            steps {
                script {
                    sh "curl -v -k -u admin:1116d22b328716f7e5806807031448f5f0 -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' -data 'IMAGE_TAG=${IMAGE_TAG}' 'http://43.205.209.23:8090/job/gitops-argocd-cd/buildWithParameters?token=gitops-config'"

                }
            }
        }
           
    }
}



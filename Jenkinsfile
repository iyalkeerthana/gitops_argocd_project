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
        stage('Push the changed deployment file to Git') {
            steps {
                script {
                    sh """
                        git config --global user.name "iyalkeerthana"
                        git config --global user.email "kavikeerthana2326@gmail.com"
                        git add deployment.yml
                        git commit -m "updated the deployment yml file"
                    """
                    withCredentials([gitUsernamePassword(credentialsId: 'GitHub', gitToolName: 'Default')]) {
    // some block
                    sh "git push https://github.com/iyalkeerthana/gitops_argocd_project.git main"
                    }
                }
            }
        }
    }
}


// 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
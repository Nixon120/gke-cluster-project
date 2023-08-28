
  pipeline {
    agent any
    environment {
        PROJECT_ID = 'chat-xe'
        LOCATION = 'us-east1'
        CREDENTIALS_ID = 'chat-xe'
        CLUSTER_NAME_PROD = 'learnk8s-cluster-prod'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("DOCKER-HUB-USERNAME/hello:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy to GKE production cluster') {
            steps{
                input message:"Proceed with final deployment?"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME_PROD, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }
}

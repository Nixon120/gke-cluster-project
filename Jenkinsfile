pipeline {
    agent any
    environment {
        PROJECT_ID = 'chat-xe'
        CLUSTER_NAME = 'learnk8s-cluster-prod'
        LOCATION = 'us-east1'
        CREDENTIALS_ID = 'chat-xe'
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
                    myapp = docker.build("nixonlauture/hello:${env.BUILD_ID}")
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
               stage('Deploy to GKE') {
            steps{
                step(
                    withKubeConfig([credentialsId: env.CREDENTIALS_ID,
                    clusterName: env.CLUSTER_NAME
                    ]) {
                   sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'  
                   sh 'chmod u+x ./kubectl'  
                   sh './kubectl apply -f deployment.yaml'
            }
        }
    }
}

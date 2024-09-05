pipeline {
    agent any
    environment {
        PROJECT_ID = 'irfan-labs'
        CLUSTER_NAME = 'sm-poc-gke-cluster'
        LOCATION = 'asia-south1-a'
        CREDENTIALS_ID = 'gke-id'
    }
    stages {
        stage("Checkout code") {
            steps {
                git url: 'https://github.com/mosasmita/custom-repo-for-GKE', branch: 'main', credentialsId: env.CREDENTIALS_ID
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("mosasmita/hello:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}
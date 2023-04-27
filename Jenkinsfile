pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "hjakhila/train-schedule"
    }
    stages {
        /*stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }*/
        stage('Build'){
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/hjakhila/cicd-pipeline-train-schedule-autodeploy.git']])
            }
        }
        stage('Build Docker Image') {
         /*   when {
                branch 'master'
            }*/
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
/*            when {
                branch 'master'
            }*/
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy'){
            environment { 
                CANARY_REPLICAS = 1
                DOCKER_IMAGE_NAME = "hjakhila/train-schedule"
                BUILD_NUMBER = "${env.BUILD_ID}"
            }
            steps {
                script {
                    withKubeConfig( clusterName: 'minikube', contextName: 'context_info', credentialsId: 'kubesa', namespace: 'default', restrictKubeConfigAccess: false, serverUrl: 'https://192.168.49.2:8443') {
                        sh 'kubectl -v=4 apply -f train-schedule-kube-canary.yml'
                    }
                }
            }
        }
    }
}

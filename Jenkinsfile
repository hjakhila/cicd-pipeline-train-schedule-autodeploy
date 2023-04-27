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
                    withKubeConfig(caCertificate: '''-----BEGIN CERTIFICATE-----
MIIDBjCCAe6gAwIBAgIBATANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwptaW5p
a3ViZUNBMB4XDTIzMDQyNjA2NTAyOVoXDTMzMDQyNDA2NTAyOVowFTETMBEGA1UE
AxMKbWluaWt1YmVDQTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBANRc
fGLNzHsOWUBGHj9V/IJEWHZkiirPXZmhOUECbeY/w4qm80DMsuHdj4erspoflgW4
AKav2h8PKdLtvF04zE0cfJER2V1V2GcJ92vL9JmUheic72d7pkGavd0dHkRqYmW6
bDKdtdr8cKbWen5QOfvJU+4phgh+uC/kqMF/IoD/iSjemczxAbw8jMFnR0D3m4+z
jJILWh8g0HwFRRsPEOHTpA/bcUlXsPq6UsX2k/lVc7dGmdMsFoTU5FqyocwfvN0t
RH/2AZuTCSeq9Rrwru9ZTCpg+JGh5w52e2vONdmdRWDj0ZUmxXdltJfrRhegsZkU
qpR5M42Uwu1FAcCX738CAwEAAaNhMF8wDgYDVR0PAQH/BAQDAgKkMB0GA1UdJQQW
MBQGCCsGAQUFBwMCBggrBgEFBQcDATAPBgNVHRMBAf8EBTADAQH/MB0GA1UdDgQW
BBSLOJSgYUUrdv9VOpghBnBfYSzayzANBgkqhkiG9w0BAQsFAAOCAQEATInYkxOj
SGNb53ENGxGVi2xAwvqQK4SoOUSM3nZ1NAYLeGLEu80CS3c6z5lKuaXsDLMoob6/
iXyhYKAkqjyt+lotNYWTDu8eIGDt0zJVgXrO3p4OVDExjiJLJtc3wiFSml9HCzoq
xZF2zocX6vLiCY2ztzmB/1aAanC1kyNrei9tuiY9w8akzJAEjO9UYWRQD+T2aIqw
cLvhEYSh7yA+Hs8XdRYWscIkVK6/uhQbW90fJwlN6/yd1MAJoqqnbPzancu/wL+X
Pk0nXRk/hJcfUfOybUb9CeME39ULgsZ7FIgwUrSVu2G0F2X79+B4FrkfiN7YeH9F
zH9VcA9xsb/mgQ==
-----END CERTIFICATE-----''', clusterName: 'minikube', contextName: 'context_info', credentialsId: 'kubesa', namespace: 'default', restrictKubeConfigAccess: false, serverUrl: 'https://192.168.49.2:8443') {
                        sh 'kubectl -v=4 apply -f train-schedule-kube-canary.yml'
                    }
                }
            }
        }
    }
}

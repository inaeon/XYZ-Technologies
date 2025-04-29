pipeline {
    agent any
    environment {
        IMAGE_NAME = "avdeshsainger/xyzproject"
    }

    stages {
        stage('code checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/inaeon/XYZ-Technologies.git'
            }
        }

        stage('code compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('code test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('code build') {
            steps {
                sh 'mvn package'
            }
        }

        stage('docker image build') {
            steps {
                sh 'cp target/XYZtechnologies-1.0.war .'
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }

        stage('docker image push') {
            steps {
                withDockerRegistry([credentialsId:"docker-id", url:""]) {
                    sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }

        stage('application deployment') {
            steps {
                sh """
                    export IMAGE_NAME=${IMAGE_NAME}
                    export BUILD_NUMBER=${BUILD_NUMBER}
                    envsubst < xyzdeploy.yaml | kubectl apply -f -
                """
                sh "kubectl apply -f xyzservice.yaml"
            }
        }
    }
}
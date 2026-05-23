pipeline {
    agent {
        label "ritik"
    }

    environment {
        IMAGE_NAME = "flask-demo"
        BUILD_TAG = "${BUILD_NUMBER}"
        ARTIFACT_NAME = "flask-demo-${BUILD_NUMBER}.tar"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME}:${BUILD_TAG} .
                """
            }
        }

        stage('Create Artifact') {
            steps {
                sh """
                docker save -o ${ARTIFACT_NAME} ${IMAGE_NAME}:${BUILD_TAG}
                """
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: '*.tar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "SUCCESS: Artifact created successfully -> ${ARTIFACT_NAME}"
        }

        failure {
            echo "Build Failed"
        }
    }
}

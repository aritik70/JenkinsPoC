pipeline {

    agent {
        label 'ritik'
    }

    environment {
        IMAGE_NAME = "flask-demo"
        BUILD_TAG = "${BUILD_NUMBER}"
        ARTIFACT_NAME = "flask-demo-${BUILD_NUMBER}.tar"
        S3_BUCKET = "jenkins-pipeline-artifacts-123"
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

        stage('Upload Artifact to S3') {
            steps {

                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds']
                ]) {

                    sh """
                    aws s3 cp ${ARTIFACT_NAME} s3://${S3_BUCKET}/
                    """
                }
            }
        }
    }

    post {

        success {

            echo "SUCCESS: Artifact uploaded successfully"

            echo "Artifact Name: ${ARTIFACT_NAME}"

            echo "S3 Path: s3://${S3_BUCKET}/${ARTIFACT_NAME}"
        }

        failure {

            echo "Build Failed"
        }
    }
}

#!groovy

/*
    Example Jenkins pipeline for building a simple docker image with Google Container Registry
    joe.richards@sapientrazorfish.com
*/

node {
    currentBuild.result = "SUCCESS"
    def GCP_PROJECT = sh returnStdout: true, script: 'gcloud config get-value project'
    def IMAGE_TAG = "gcr.io/${GCP_PROJECT}/${env.APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"

    checkout scm

    try {
        stage('Build Docker image using Google Container Registry') {
            print "Publishing container to gcr.io/${GCP_PROJECT}/${env.APP_NAME}"
            sh "gcloud container builds submit . --tag ${IMAGE_TAG}"
        }

        stage('Testing image') {
            sh "gcloud docker -- pull ${IMAGE_TAG}"
            sh "docker run ${IMAGE_TAG} echo Hello World"
        }

        stage('Deploy') {
            sh "gcloud docker -- pull ${IMAGE_TAG}"
            sh "docker run ${IMAGE_TAG}"
        }
    }

    catch (err) {
        currentBuild.result = "FAILURE"
        throw err
    }
}

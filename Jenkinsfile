#!groovy

/*
    Example Jenkins pipeline for building a simple docker image with Google Container Registry
    joe.richards@sapientrazorfish.com
*/

node {
    currentBuild.result = "SUCCESS"
    def GCP_PROJECT = sh (returnStdout: true, script: 'gcloud config get-value project').trim()
    def BUILD_CONFIG = readYaml file: build.yaml
    def IMAGE_NAME = BUILD_CONFIG.image_name
    def VERSION = BUILD_CONFIG.version
    def IMAGE_TAG = "gcr.io/${GCP_PROJECT}/${IMAGE_NAME}:${env.BRANCH_NAME}-${VERSION}.${env.BUILD_NUMBER}"

    checkout scm

    try {
        stage('Printing out environment info') {
            sh "printenv"
        }

        stage('Build Docker image using Google Container Registry') {
            print "Building container [gcr.io/${GCP_PROJECT}/${IMAGE_NAME}]"
            sh "gcloud container builds submit . --tag ${IMAGE_TAG}"
        }

        stage('Testing image') {
            sh "gcloud docker -- pull ${IMAGE_TAG}"
            sh "docker run ${IMAGE_TAG} echo Hello World"
        }

        stage('Run container') {
            sh "gcloud docker -- pull ${IMAGE_TAG}"
            sh "docker run ${IMAGE_TAG}"
        }
    }

    catch (err) {
        currentBuild.result = "FAILURE"
        throw err
    }
}

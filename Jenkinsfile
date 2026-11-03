pipeline {
    agent any

    environment {
        DOCKERHUB = credentials('dockerhub')
        TAG       = '1.0'
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Build images') {
            steps {
                dir('backend')  { sh "docker build -t ${DOCKERHUB_USR}/appgestion-backend:${TAG} ." }
                dir('frontend') { sh "docker build -t ${DOCKERHUB_USR}/appgestion-frontend:${TAG} ." }
            }
        }

        stage('Run compose (test)') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d --build'
                sh 'docker compose ps'
            }
        }

        stage('Push images') {
            steps {
                sh 'echo "$DOCKERHUB_PSW" | docker login -u "$DOCKERHUB_USR" --password-stdin'
                sh "docker push ${DOCKERHUB_USR}/appgestion-backend:${TAG}"
                sh "docker push ${DOCKERHUB_USR}/appgestion-frontend:${TAG}"
            }
        }
    }

    post {
        success { echo 'OK : images buildees et poussees' }
        failure { echo 'ECHEC du pipeline' }
    }
}

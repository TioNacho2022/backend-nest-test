pipeline {
    agent any

    environment {
        NPM_CONFIG_CACHE = "${WORKSPACE}/.npm"
    }

    stages {
        stage("Instalación de dependencias y pruebas") {
            agent {
                docker {
                    image 'node:22'
                }
            }
            steps {
                script {
                    sh 'npm install'
                    sh 'npm run test:cov'
                }
            }
        }

        stage("Build de la aplicación") {
            agent {
                docker {
                    image 'node:22'
                }
            }
            steps {
                sh 'npm run build'
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    def imageName = "gcr.io/tu-proyecto/backend-nest-test-ABC"
                    def tagLatest = "${imageName}:latest"
                    def tagBuild = "${imageName}:${BUILD_NUMBER}"

                    sh """
                        docker build -t ${tagLatest} .
                        docker tag ${tagLatest} ${tagBuild}
                    """
                }
            }
        }

        stage("Push to GCR") {
            steps {
                script {
                    def imageName = "gcr.io/tu-proyecto/backend-nest-test-ABC"
                    def tagLatest = "${imageName}:latest"
                    def tagBuild = "${imageName}:${BUILD_NUMBER}"

                    sh """
                        gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
                        gcloud auth configure-docker
                        docker push ${tagLatest}
                        docker push ${tagBuild}
                    """
                }
            }
        }

        stage("Deploy en Kubernetes") {
            steps {
                script {
                    def imageName = "gcr.io/tu-proyecto/backend-nest-test-ABC:${BUILD_NUMBER}"
                    sh """
                        kubectl set image deployment/backend backend=${imageName} --namespace=default
                    """
                }
            }
        }
    }
}

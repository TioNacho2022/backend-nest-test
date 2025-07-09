pipeline {
    agent any

    environment {
        NPM_CONFIG_CACHE = "${WORKSPACE}/.npm"
        dockerImagePrefix = "us-west1-docker.pkg.dev/lab-agibiz/docker-repository"
        registry = "https://us-west1-docker.pkg.dev"
        registryCredentials = "gcp-registry"
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
                script{
                    docker.withRegistry("${registry}", registryCredentials) {
                    sh "docker build -t backend-nest-cmd ."
                    sh "docker tag backend-nest-cmd ${dockerImagePrefix}/backend-nest-cmd"
                    sh "docker push ${dockerImagePrefix}/backend-nest-cmd"
                }
                
                
            }
        }
    }
}

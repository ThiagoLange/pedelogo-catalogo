pipeline {
    agent any

    stages {
        stage('heckout Source') {
            steps {
                git url: 'https://github.com/ThiagoLange/pedelogo-catalogo.git', branch: 'main'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    dockerapp = docker.build("thiagolange/api-produto2:${env.BUILD_ID}",
                        '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }

                }
            }
        }

        stage('Deploy Kubernetes') {
            agent {
                kubernetes {
                    cloud 'kubernetes'
                }
            }
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                script {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/api/deployment.yaml'
                    sh 'cat ./k8s/api/deployment.yaml'
                    kubernetes(configs: '**/k8s/**', kubeconfigId: 'kube')
                }
            }
        }

    }
}
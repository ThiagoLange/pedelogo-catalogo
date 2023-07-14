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
                    dockerapp = docker.build("thiago_lange/api-produto:${env.BUILD_ID}",
                        '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                        docker.witRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }

                }
            }
        }

    }
}
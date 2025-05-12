pipeline {
    agent any
    
    triggers {
        githubPush()
    }
    environment {
        IMAGE_NAME = "gcp-web"
        CONTAINER_NAME = "gcp-web"
        PORT = "9001"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/master']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/vsantosweb/gcp-web.git',
                    ]]
                ])
            }
        }
        
        
        stage('Build Docker Image') {
            steps {
                sh '''
                    echo ">> Construindo imagem Docker"
                    docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    echo ">> Parando e removendo container antigo (se existir)"
                    docker stop $CONTAINER_NAME || true
                    docker rm -f $CONTAINER_NAME || true

                    echo ">> Subindo novo container"
                    docker run -d \
                      --name $CONTAINER_NAME \
                      -e APP_PORT=8002 \
                      -e APP_ENV=local \
                      -p 8002:8002 \
                      $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deploy finalizado com sucesso!'
        }
        failure {
            echo '❌ Algo deu errado no pipeline.'
        }
    }
}

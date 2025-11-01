pipeline {
    agent any
    stages {
        stage('Test Frontend') {
            steps {
                echo 'Testing Fronted...'
                sh '''
                    docker build -t complex-client-test-image -f ./client/Dockerfile.dev ./client 
                    docker run --rm --name complex-client-test-container complex-client-test-image npm run test -- --coverage
                '''
            }
        }

        stage('Build All Images') {
            steps {
                echo 'Building...'
                sh '''
                    docker build -t complex-client-image ./client 
                    docker build -t complex-nginx-image ./nginx 
                    docker build -t complex-server-image ./server 
                    docker build -t complex-worker-image ./worker 
                '''  
            }
        }

        stage('Login to Docker Hub and Push Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'DOCKER_HUB_CREDS', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "docker login -u ${DOCKER_USERNAME} --password-stdin <<< ${DOCKER_PASSWORD}"
                        // Build and tag your Docker image here
                        sh "docker push aasharmehmood/complex-client-image"
                        sh "docker push aasharmehmood/complex-nginx-image"
                        sh "docker push aasharmehmood/complex-server-image"
                        sh "docker push aasharmehmood/complex-worker-image"
                        sh "docker logout"
                    }
                }
            }
        }
    }
}

pipeline {
    agent any
    stages {
        
    }
}
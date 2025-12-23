pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDS = credentials('DOCKER_HUB_CREDS')
        AWS_EB_APP_NAME = 'complex'
        AWS_EB_ENV_NAME = 'Complex-env'
        AWS_REGION = 'eu-north-1'
    }
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
                    docker build -t aasharmehmood/complex-client:latest ./client
                    docker build -t aasharmehmood/complex-nginx:latest ./nginx
                    docker build -t aasharmehmood/complex-server:latest ./server
                    docker build -t aasharmehmood/complex-worker:latest ./worker 
                '''  
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh '''
                    echo $DOCKER_HUB_CREDS_PSW | docker login -u $DOCKER_HUB_CREDS_USR --password-stdin
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                    docker push aasharmehmood/complex-client:latest
                    docker push aasharmehmood/complex-nginx:latest
                    docker push aasharmehmood/complex-server:latest
                    docker push aasharmehmood/complex-worker:latest
                    docker logout
                '''
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', 
                                    credentialsId: 'aashar-aws-creds', 
                                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                        source ~/.venvs/ebcli/bin/activate
                        eb init ${AWS_EB_APP_NAME} --platform "Docker" --region ${AWS_REGION}
                        eb use ${AWS_EB_ENV_NAME}
                        eb deploy
                    '''
                }
            }
        }

        // stage('Deploy to Elastic Beanstalk') {
        //     steps {
        //         step([$class: 'AWSEBDeploymentBuilder',
        //             credentialId: "aashar-aws-creds",
        //             applicationName: "${AWS_EB_APP_NAME}",
        //             environmentName: "${AWS_EB_ENV_NAME}",
        //             awsRegion: "${AWS_REGION}",
        //             versionLabel: "build-${env.BUILD_NUMBER}",
        //             rootObject: '.',
        //             includes: '**',
        //             excludes: '.git/**, node_modules/**'
        //         ])
        //     }
        // }

    }
}



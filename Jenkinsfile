pipeline {
    agent any 

    environment {
        MONGO_USER_CRED = credentials('mongo-user')
        MONGO_PASSWORD_CRED = credentials('mongo-password')
        
        MONGO_USER = "${MONGO_USER_CRED}"
        MONGO_PASSWORD = "${MONGO_PASSWORD_CRED}"
        MONGO_PORT = "27017"               
        MONGO_EXPRESS_PORT = "8081"        
    }

    stages {
        stage('Build') { 
            steps {
                echo 'Stage Build'
                sh 'docker-compose build'
                sh 'docker-compose up -d mongo app'
            }
            post {
                success {
                    echo 'Build was successful'
                }
                failure {
                    echo 'Build failed'
                }
            }
        }
        stage('Test') { 
            steps {
                echo 'Stage Test'
                sh 'docker-compose run --rm --service-ports test'
            }
            post {
                success {
                    echo 'Test was successful'
                }
                failure {
                    echo 'Test failed'
                }
            }
        }
        stage('Push') { 
            steps {
                echo 'Stage Push'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh '''
                            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                            docker tag loggingapp:latest karol301/loggingapp:latest
                            docker push karol301/loggingapp:latest
                        '''
                }
            }
            post {
                success {
                    echo 'Push was successful'
                }
                failure {
                    echo 'Push failed'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline ended.'
        }
    }
}

pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'lay21/flask-starter'
        // PATH = "C:\\python339;C:\\python339\\Scripts;${env.PATH}"
    }

    stages{
        
        stage('Install Dependencies') {
           steps {
                sh '''

                    python3 -m pip install --upgrade pip
                    python3 -m pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''

                    python3 -m pytest
                '''
            }
        }

         stage('Docker Build') {
            steps {
                sh 'docker build -t lay21/flask-starter .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo Logging in to DockerHub...
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                    docker push %DOCKER_IMAGE%
                    '''
                }
            }
        }
        stage('Deploy to Staging') {
            steps {
                script {
                    sh "docker run -d -p 5000:5000 %DOCKER_IMAGE%:%BUILD_NUMBER%"
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                input 'Deploy to Production?'
                script {
                    sh "docker run -d -p 5000:5000 %DOCKER_IMAGE%:%BUILD_NUMBER%"
                }
            }
        }
    }

    }
    post {
        always {
            cleanWs()
        }
    }


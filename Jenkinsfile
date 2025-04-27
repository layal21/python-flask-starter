pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'lay21/flask-starter'
        // PATH = "C:\\Python39;C:\\Python39\\Scripts;${env.PATH}"
    }

    stages{
        
        stage('Install Dependencies') {
           steps {
                bat '''
                    set PYTHONHOME=
                    set PYTHONPATH=
                    C:\\Python312\\python.exe -m pip install --upgrade pip
                    C:\\Python312\\python.exe -m pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                bat '''
                    set PYTHONHOME=
                    set PYTHONPATH=
                    C:\\Python312\\python.exe -m pytest
                '''
            }
        }

         stage('Docker Build') {
            steps {
                bat 'docker build -t lay21/flask-starter .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat '''
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
                    bat "docker run -d -p 5000:5000 %DOCKER_IMAGE%:%BUILD_NUMBER%"
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                input 'Deploy to Production?'
                script {
                    bat "docker run -d -p 5000:5000 %DOCKER_IMAGE%:%BUILD_NUMBER%"
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


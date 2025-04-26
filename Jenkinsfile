pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'lay21/flask-startere'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/layal21/python-flask-starter.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install Python dependencies
                    sh 'pip install -r requirements.txt'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run your tests (e.g., using pytest)
                    sh 'pytest'
                }
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
                    docker push yourdockerhubusername/myflaskapp
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
    post {
        always {
            cleanWs()
        }
    }
}

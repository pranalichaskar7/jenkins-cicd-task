
pipeline {
    agent any

    triggers {
        pollSCM('H/2 * * * *')
    }

    environment {
        IMAGE_NAME = 'jenkins-cicd-app'
    }

    stages {
        stage('Build') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Test') {
            steps {
                bat '''
                    docker rm -f ci-cd-test 2>NUL
                    docker run -d --name ci-cd-test -p 8081:80 %IMAGE_NAME%
                    powershell -NoProfile -Command "$r = Invoke-WebRequest -Uri http://localhost:8081 -UseBasicParsing; if ($r.StatusCode -ne 200) { exit 1 }"
                    docker rm -f ci-cd-test
                '''
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                    docker rm -f ci-cd-app 2>NUL
                    docker run -d --restart unless-stopped --name ci-cd-app -p 8082:80 %IMAGE_NAME%
                '''
            }
        }
    }
}

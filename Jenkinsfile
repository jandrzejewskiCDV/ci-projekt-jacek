pipeline {
    agent any

    stages {
        stage('Info') {
            steps {
                echo "Galaz: ${env.GIT_BRANCH}"
                echo "Build: ${env.BUILD_NUMBER}"
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                sh 'python3 test_app.py'
            }
        }
        stage('Build') {
            steps {
                echo 'Building..'
                sh 'docker build -t flask .'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
                sh 'docker ps -q --filter name="flask" | xargs -r docker rm -f'
                sh 'docker run -d -p 5000:5000 --name flask flask'
            }
        }
    }

    post {
        success {
            echo "OK — galaz: ${env.GIT_BRANCH}"
        }
        failure {
            echo 'BLAD! Sprawdz logi.'
        }
    }
}
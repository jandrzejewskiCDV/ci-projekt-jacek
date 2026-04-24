pipeline {
    agent any

    parameters {
        choice(
            name: 'SRODOWISKO',
            choices: ['dev', 'staging', 'prod'],
            description: 'Srodowisko docelowe'
        )
    }

    options {
        timeout(time: 15, unit: 'MINUTES')
    }

    stages {
        stage('Info') {
            steps {
                echo "Galaz: ${env.GIT_BRANCH}"
                echo "Build: ${env.BUILD_NUMBER}"
                echo "Deploy na: ${params.SRODOWISKO}"
            }
        }
        stage('Test') {
            when {
                expression {
                    env.GIT_BRANCH != 'origin/main'
                }
            }
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
        stage('Verify-Deploy'){
            options {
                timeout(time: 5, unit: 'MINUTES')
            }
            when {
                expression { params.SRODOWISKO == 'prod' }
            }
            steps {
                input message: 'Czy na pewno wdrozyc na PRODUKCJE?',
                ok: 'Tak, wdrazaj!'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
                sh 'docker rm -f flask 2> /dev/null'
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
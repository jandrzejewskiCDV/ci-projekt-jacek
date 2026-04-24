pipeline {
    agent any

    environment{
        APP = "apka"
        VERSION = "1.0"
        BUILD_IMAGE="flask-${env.BUILD_NUMBER}"
    }

    parameters {
        choice(
            name: 'SRODOWISKO',
            choices: ['dev', 'staging', 'prod'],
            description: 'Srodowisko docelowe'
        )
    }

    options {
        timeout(time: 20, unit: 'MINUTES')
    }

    stages {
        stage('Info') {
            steps {
                echo "nazwa: ${env.APP}"
                echo "wersja: ${params.VERSION}"
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
                sh "docker build -t ${BUILD_IMAGE} ."
            }
        }
        stage('Analisys') {
            steps {
                echo 'Analyzing..'
                sh "docker inspect ${BUILD_IMAGE}"
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
                sh "docker run -d -p 5000:5000 --name flask ${BUILD_IMAGE}"
            }
        }
        stage('Deploy-Success-Notify'){
            when {
                expression { params.SRODOWISKO == 'dev' }
            }
            steps {
                echo 'Sukces na dev'
            }
        }
    }

    post {
        success {
            echo "OK — ${env.BUILD_NUMBER} / ${params.SRODOWISKO} / ${env.BUILD_IMAGE}"
        }
        failure {
            echo 'BLAD! Sprawdz logi.'
        }
    }
}
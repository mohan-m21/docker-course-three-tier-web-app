pipeline {
    agent any

    // Optional: environment variables to avoid name conflicts on shared Jenkins
    environment {
        COMPOSE_PROJECT_NAME = 'three-tier-quotes'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Images') {
            steps {
                sh 'docker compose build --no-cache'   // clean build in CI
                // Alternative if your setup still needs hyphen: sh 'docker-compose build --no-cache'
            }
        }

        stage('Start Stack') {
            steps {
                sh 'docker compose up -d'
                sleep time: 45, unit: 'SECONDS'   // Give MySQL + Flask time to initialize
            }
        }

        // Optional future stage – add when you write tests
        // stage('Backend Tests') {
        //     when { fileExists 'api/requirements.txt' }
        //     steps {
        //         dir('api') {
        //             sh '''
        //                 pip install -r requirements.txt pytest
        //                 pytest
        //             '''
        //         }
        //     }
        // }
    }

    post {
        always {
            // Clean up – very important on Jenkins agents to free ports/disk
            sh 'docker compose down --remove-orphans || true'
            sh 'docker compose rm -f || true'
            // Optional: remove images to save space
            // sh 'docker compose down --rmi local || true'
        }

        success {
            echo "Pipeline success → 3-tier app built and verified"
        }

        failure {
            echo "Pipeline failed – check logs for build/startup issues"
        }
    }
}

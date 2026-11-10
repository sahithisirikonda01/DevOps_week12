
pipeline {
    agent any

    stages {

        stage('Run Selenium Tests with pytest') {
            steps {
                echo "Running Selenium Tests using pytest"

                bat 'python -m pip install -r requirements.txt'
                bat 'start "" /B python app.py'
                bat 'ping 127.0.0.1 -n 6 > nul'
                bat 'python -m pytest -v tests/test_registration.py'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker Image"

                bat 'docker build -t sahithisirikonda01/week12-registration:v1 .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_TOKEN'
                    )
                ]) {
                    bat '''
                        @echo off
                        echo %DOCKERHUB_TOKEN%| docker login -u %DOCKERHUB_USER% --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                echo "Pushing Docker Image"

                bat 'docker push sahithisirikonda01/week12-registration:v1'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying Application to Kubernetes"

                bat 'kubectl apply -f deployment.yaml'
                bat 'kubectl apply -f service.yaml'
                bat 'kubectl rollout status deployment/seleniumtestdemoapp1-deployment --timeout=120s'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Please check the Jenkins logs.'
        }
    }
}

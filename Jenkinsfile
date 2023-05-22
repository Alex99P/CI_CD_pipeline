pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    echo "Testing the application.."
                    echo "Executing pipeline for branch $BRANCH_NAME"
                }
            }
        }
        stage('Build') {
            steps {
                 script {
                    echo "Testing the application.."
                }
            }
        }
        stage('Deploy') {
            steps {
                echo "Testing the application.."
            }
        }
    }
}
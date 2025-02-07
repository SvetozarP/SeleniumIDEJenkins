pipeline {
    agent any  // Runs on any available Windows agent

    environment {
        DOTNET_VERSION = '6.0'  // Change to your required .NET version
        SOLUTION_FILE = 'SeleniumIde.sln'  // Change to your solution file
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo 'Checking out the code...'
                    checkout scm
                }
            }
        }

        stage('Restore Dependencies') {
            steps {
                script {
                    echo 'Restoring NuGet packages...'
                    bat "dotnet restore ${SOLUTION_FILE}"
                }
            }
        }

        stage('Build Solution') {
            steps {
                script {
                    echo 'Building the project...'
                    bat "dotnet build ${SOLUTION_FILE}"
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    echo 'Running tests...'
                    bat "dotnet test ${SOLUTION_FILE}"
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and Tests Passed Successfully!'
        }
        failure {
            echo '❌ Build or Tests Failed! Check logs.'
        }
    }
}
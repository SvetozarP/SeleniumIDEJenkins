pipeline {
    agent any  // Runs on any available Windows agent

    environment {
        DOTNET_VERSION = '6.0'  // Change to your required .NET version
        CHROME_VERSION = '133.0.6943.5300'  // Set required Chrome version
        CHROMEDRIVER_VERSION = '133.0.6943.5300'  // Match ChromeDriver to Chrome version
        SOLUTION_FILE = 'SeleniumIde.sln'  // Change to your solution file
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo '🔄 Checking out the code...'
                    checkout scm
                }
            }
        }

        stage('Set Up .NET Core') {
            steps {
                script {
                    echo '🔧 Installing .NET SDK...'
                    bat """
                    echo Checking .NET version...
                    dotnet --version || choco install dotnet-sdk-${DOTNET_VERSION} -y
                    dotnet --version
                    """
                }
            }
        }

        stage('Uninstall Current Chrome') {
            steps {
                script {
                    echo '❌ Uninstalling current Chrome version...'
                    bat """
                    echo Checking if Chrome is installed...
                    wmic product where "name like 'Google Chrome%%'" call uninstall /nointeractive || echo Chrome not found
                    """
                }
            }
        }

        stage('Install Chrome v133.0.6943.5300') {
            steps {
                script {
                    echo '🌍 Installing required Chrome version...'
                    bat """
                    choco install googlechrome --version=${CHROME_VERSION} -y
                    """
                }
            }
        }

        stage('Download and Install ChromeDriver') {
            steps {
                script {
                    echo '🚗 Downloading ChromeDriver...'
                    bat """
                    powershell -Command "& {Invoke-WebRequest -Uri https://chromedriver.storage.googleapis.com/${CHROMEDRIVER_VERSION}/chromedriver_win32.zip -OutFile chromedriver.zip}"
                    powershell -Command "& {Expand-Archive -Path chromedriver.zip -DestinationPath C:\\chromedriver\\ -Force}"
                    setx PATH "%PATH%;C:\\chromedriver\\"
                    """
                }
            }
        }

        stage('Restore Dependencies') {
            steps {
                script {
                    echo '📦 Restoring NuGet packages...'
                    bat "dotnet restore ${SOLUTION_FILE}"
                }
            }
        }

        stage('Build Solution') {
            steps {
                script {
                    echo '🛠️ Building the project...'
                    bat "dotnet build ${SOLUTION_FILE} --configuration Release --no-restore"
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    echo '🧪 Running tests...'
                    bat "dotnet test ${SOLUTION_FILE} --no-build --verbosity normal"
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

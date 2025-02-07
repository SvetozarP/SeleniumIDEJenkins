pipeline {
    agent any  // Runs on any available Windows agent

    environment {
        DOTNET_VERSION = '6.0.100'  // Change to your required .NET version
        CHROME_VERSION = '133.0.6943.5300'  // Set required Chrome version
        CHROMEDRIVER_VERSION = '133.0.6943.5300'  // Match ChromeDriver to Chrome version
        CHROME_INSTALL_PATH = 'C:\\Program Files\\Google\\Chrome\\Application'
        CHROMEDRIVER_PATH = '"C:\\Program Files\\Google\\Chrome\\Application\\chromedriver.exe"'
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo '🔄 Checking out the code...'
                    git branch: 'main', url: 'https://github.com/SvetozarP/SeleniumIDEJenkins.git'
                }
            }
        }

        stage('Set Up .NET Core') {
            steps {
                script {
                    echo '🔧 Installing .NET SDK...'
                    bat """
                    echo Checking .NET version...
                    choco install dotnet-sdk-${DOTNET_VERSION}
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
                    choco uninstall googlechrome -y
                    """
                }
            }
        }

        stage('Install Chrome v133.0.6943.5300') {
            steps {
                script {
                    echo '🌍 Installing required Chrome version...'
                    bat """
                    choco install googlechrome --version=${CHROME_VERSION} -y --alllow-downgrade --ignore-checksums
                    """
                }
            }
        }

        stage('Download and Install ChromeDriver') {
            steps {
                bat '''
                echo Downloading ChromeDriver version %CHROMEDRIVER_VERSION%
                powershell -command "Invoke-WebRequest -Uri https://chromedriver.storage.googleapis.com/%CHROMEDRIVER_VERSION%/chromedriver_x64.zip -OutFile chromedriver.zip -UseBasicParsing"
                powershell -command "Expand-Archive -Path chromedriver.zip -DestinationPath ."
                powershell -command "Move-Item -Path .\\chromedriver.exe -Destination '%CHROME_INSTALL_PATH%\\chromedriver.exe' -Force"
                '''
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
                    bat 'dotnet test ${SOLUTION_FILE} --logger "trx;logfilename=TestResults.trx"'
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifcats: '**/TestResults/*.trx', allowEmptyArchive: true
            junit '**/TestResults/*.trx'
        }
        success {
            echo '✅ Build and Tests Passed Successfully!'
        }
        failure {
            echo '❌ Build or Tests Failed! Check logs.'
        }
    }
}

pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS_ID = 'azure-service-principal'
        RESOURCE_GROUP = 'myResourceGroup'
        APP_SERVICE_NAME = 'myPythonAppAvinash01'
        PATH = "C:\\Users\\Lenovo\\AppData\\Local\\Programs\\Python\\Python312;C:\\Users\\Lenovo\\AppData\\Local\\Programs\\Python\\Python312\\Scripts;${env.PATH}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Avinash739jecrc/pythonwenappjenkins.git'
            }
        }

        stage('Set Up Python') {
            steps {
                bat 'python -m venv venv'
                bat 'call venv\\Scripts\\activate && pip install -r requirements.txt'
            }
        }

        stage('Prepare Publish Folder') {
            steps {
                bat 'if exist publish (rmdir /s /q publish)'
                bat 'mkdir publish'
                bat 'copy *.py publish\\'
                bat 'if exist requirements.txt copy requirements.txt publish\\'
            }
        }

        stage('Zip Files') {
            steps {
                bat 'powershell Compress-Archive -Path publish\\* -DestinationPath publish.zip -Force'
            }
        }

        stage('Deploy to Azure') {
            steps {
                withCredentials([azureServicePrincipal(credentialsId: AZURE_CREDENTIALS_ID)]) {
                    bat 'az login --service-principal -u %AZURE_CLIENT_ID% -p %AZURE_CLIENT_SECRET% --tenant %AZURE_TENANT_ID%'
                    bat 'az webapp deployment source config-zip --resource-group %RESOURCE_GROUP% --name %APP_SERVICE_NAME% --src publish.zip'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}

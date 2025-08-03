pipeline {
    agent any

    environment {
        AZURE_CREDENTIALS = credentials('Azure-rishhi.dev')
        FUNCTION_APP_NAME = 'rishhi-func-demo'
        RESOURCE_GROUP = 'exo-code'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building the application...'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'npm test'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to Azure...'
                writeFile file: 'azureauth.json', text: "${AZURE_CREDENTIALS}"
                sh '''
                    az login --service-principal --username $(jq -r .clientId azureauth.json) \
                             --password $(jq -r .clientSecret azureauth.json) \
                             --tenant $(jq -r .tenantId azureauth.json)

                    zip -r function.zip .
                    az functionapp deployment source config-zip \
                        --resource-group $RESOURCE_GROUP \
                        --name $FUNCTION_APP_NAME \
                        --src function.zip
                '''
            }
        }
    }
}

pipeline {
    agent any

    environment {
        AZURE_CREDENTIALS = credentials('Azure-rishhi.dev')
        FUNCTION_APP_NAME = 'rishhi-func-demo'
        RESOURCE_GROUP = 'exo-code'
        TENANT_ID = '31e2cd05-f1eb-4c69-ae74-4ba8110fd035'
    }

    tools {
        nodejs 'node'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Installing Node.js dependencies...'
                    sh 'npm install'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo '🧪 Running tests...'
                    sh 'npm test'
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    echo '📁 Zipping function for deployment...'
                    sh '''
                        rm -f function.zip
                        zip -r function.zip * -x "*.git*" "node_modules/*" "*.zip"
                    '''
                }
            }
        }

        stage('Deploy to Azure') {
            steps {
                script {
                    echo '☁️ Deploying Azure Function to cloud...'
                    sh """
                        az login --service-principal \
                          -u $AZURE_CREDENTIALS_USR \
                          -p $AZURE_CREDENTIALS_PSW \
                          --tenant $TENANT_ID && \
                        az functionapp deployment source config-zip \
                          --resource-group $RESOURCE_GROUP \
                          --name $FUNCTION_APP_NAME \
                          --src function.zip
                    """
                }
            }
        }
    }

    post {
        success {
            echo '🎉 CI/CD Pipeline completed successfully and function deployed!'
        }
        failure {
            echo '❌ Pipeline failed. Check the logs for errors.'
        }
    }
}

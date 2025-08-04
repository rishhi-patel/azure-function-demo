pipeline {
    agent any

    environment {
        FUNCTION_APP_NAME = 'rishhi-func-demo'
        RESOURCE_GROUP = 'exo-code'
        TENANT_ID = '31e2cd05-f1eb-4c69-ae74-4ba8110fd035'
    }

    tools {
        nodejs 'node'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Fetching code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    echo '📦 Installing Node.js dependencies...'
                    sh 'npm install'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo '🧪 Running tests...'
                    sh 'npm test --prefix src/functions'
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    echo '📁 Zipping function for deployment...'
                    sh '''
                        rm -f function.zip
                        cd src/functions
                        zip -r ../../function.zip * -x "*.git*" "*@tmp*" "*.DS_Store"
                        cd ../..
                        echo "----- Zip file content -----"
                        unzip -l function.zip
                    '''
                }
            }
        }

        stage('Deploy to Azure') {
            steps {
                script {
                    echo '☁️ Deploying Azure Function to cloud...'
                    withCredentials([usernamePassword(credentialsId: 'Azure-rishhi.dev',
                                                     usernameVariable: 'AZURE_CLIENT_ID',
                                                     passwordVariable: 'AZURE_CLIENT_SECRET')]) {
                        sh '''
                            az login --service-principal \
                              -u $AZURE_CLIENT_ID \
                              -p $AZURE_CLIENT_SECRET \
                              --tenant 31e2cd05-f1eb-4c69-ae74-4ba8110fd035

                            az functionapp deployment source config-zip \
                              --resource-group exo-code \
                              --name rishhi-func-demo \
                              --src function.zip
                        '''
                    }
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

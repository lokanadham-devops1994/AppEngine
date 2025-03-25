pipeline {
    agent any

    environment {
        PROJECT_ID = 'morning-batch-449809'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/lokanadham-devops1994/AppEngine.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh '''
                    # Ensure Python and dependencies are available
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Deploy to Google App Engine') {
            steps {
                script {
                    sh 'gcloud --version'
                    sh 'pwd && ls -l'

                    # Authenticate using correct file path
                    sh 'gcloud auth activate-service-account --key-file="$GOOGLE_APPLICATION_CREDENTIALS"'

                    # Set project ID
                    sh 'gcloud config set project $PROJECT_ID'

                    # Check if app.yaml exists
                    sh '''
                    if [ ! -f app.yaml ]; then
                        echo "Error: app.yaml not found!"
                        exit 1
                    fi
                    gcloud app deploy app.yaml --quiet
                    '''
                }
            }
        }
    }

    post {
        always {
            node {
                echo 'Cleaning up...'
                cleanWs()
            }
        }

        success {
            echo 'Deployment successful!'
        }

        failure {
            echo 'Deployment failed!'
        }
    }
}

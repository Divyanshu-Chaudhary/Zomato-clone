pipeline {
    agent any

    environment {
        SONAR_HOME             = tool 'sonar-scanner'
        IMAGE_NAME             = 'zomato-django-app'
        CONTAINER_NAME         = 'zomato_app'

        // Folder to avoid overwriting the Jenkinsfile repo workspace checkout
        APP_DIR                = 'app_src'

        // IMPORTANT: This credential MUST exist in Jenkins (Username/Password, password = GitHub PAT)
        GITHUB_CREDENTIALS_ID  = 'github-pat'

        APP_REPO_URL           = 'https://github.com/Divyanshu-Chaudhary/Zomato_Clone_Food_Delivery_Web_Application.git'
        APP_REPO_BRANCH        = 'main'
    }

    stages {
        stage('Clone Application Repository') {
            steps {
                dir("${APP_DIR}") {
                    git credentialsId: "${GITHUB_CREDENTIALS_ID}",
                        url: "${APP_REPO_URL}",
                        branch: "${APP_REPO_BRANCH}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir("${APP_DIR}") {
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }

        stage('Stop Existing Container') {
            steps {
                sh '''
                  docker stop $CONTAINER_NAME || true
                  docker rm $CONTAINER_NAME || true
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                  docker run -d \
                    --name $CONTAINER_NAME \
                    -p 8000:8000 \
                    $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success { echo '✅ Zomato Django App deployed successfully!' }
        failure { echo '❌ Deployment failed. Check logs.' }
    }
}

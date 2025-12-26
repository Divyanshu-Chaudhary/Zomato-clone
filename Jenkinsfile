pipeline {
    agent any

    environment {
        SONAR_HOME      = tool 'sonar-scanner'
        IMAGE_NAME      = "zomato-django-app"
        CONTAINER_NAME  = "zomato_app"
        APP_DIR         = "app_src"
    }

    stages {

        // Jenkins already checks out the Jenkinsfile repo in "Declarative: Checkout SCM".
        // This stage clones your actual application repo into a subfolder (prevents workspace overwrite).
        stage('Clone Application Repository') {
            steps {
                dir("${APP_DIR}") {
                    // Create a Jenkins credential like "github-pat" (Username+Password where password = GitHub PAT)
                    git credentialsId: 'github-pat',
                        url: 'https://github.com/Divyanshu-Chaudhary/Zomato_Clone_Food_Delivery_Web_Application.git',
                        branch: 'main'
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
        success { echo "✅ Zomato Django App deployed successfully!" }
        failure { echo "❌ Deployment failed. Check logs." }
    }
}

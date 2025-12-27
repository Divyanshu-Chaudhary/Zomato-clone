pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub-creds')
        DOCKER_IMAGE = "divyanshu067/zomato-clone"
        SONAR_HOME = tool 'sonar-scanner'  // This is the scanner TOOL
    }
    
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Divyanshu-Chaudhary/Zomato-clone.git'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                // Leave empty to use default server, OR use the actual server name from:
                // Jenkins -> Manage Jenkins -> Configure System -> SonarQube servers -> Name
                withSonarQubeEnv('sonar') {
                    sh '''
                        $SONAR_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=zomato-clone \
                        -Dsonar.projectName="Zomato Clone" \
                        -Dsonar.sources=. \
                        -Dsonar.language=py
                    '''
                }
            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} -t ${DOCKER_IMAGE}:latest ."
            }
        }
        
        stage('Docker Push') {
            steps {
                sh '''
                    echo $DOCKER_HUB_CREDENTIALS_PSW | docker login -u $DOCKER_HUB_CREDENTIALS_USR --password-stdin
                    docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    docker push ${DOCKER_IMAGE}:latest
                '''
            }
        }
        
        stage('Update Manifest') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-cred', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                    sh '''
                        git clone https://${GIT_USER}:${GIT_TOKEN}@github.com/Divyanshu-Chaudhary/zomato-manifest.git
                        cd zomato-manifest
                        sed -i "s|divyanshu067/zomato-clone:.*|divyanshu067/zomato-clone:${BUILD_NUMBER}|g" deployment.yaml
                        git config user.email "jenkins@ci.com"
                        git config user.name "Jenkins"
                        git add .
                        git commit -m "Update image to build ${BUILD_NUMBER}"
                        git push
                    '''
                }
            }
        }
    }
    
    post {
        always {
            sh 'docker logout || true'
        }
    }
}

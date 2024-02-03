pipeline {
    agent any
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
        DOCKERHUB_CREDENTIALS=credentials('716274dc-f41e-4e10-9f58-f501c9063a39')
    }
    stages{
       stage('Clean workspace'){
           steps{
            cleanWs()
           }
       }
       stage('Git Checkout'){
            steps{    
               checkout scmGit(branches: [[name: '*/dev'], [name: '*/qa'], [name: '*/prod']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AfriTech-DevOps/Ifeoma-RapheeBeauty.git']])
            
            }
        }
        stage('Sonarqube Analysis'){
            steps{
                script{
                    withSonarQubeEnv('sonar-server') {
                        sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Rapheebeauty -Dsonar.projectName=Rapheebeauty"
                }
            }
        }
        }
        stage('Quality Gate'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }

        stage('Login to DockerHUB'){
            steps{
                sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
                echo "login succeeded"
            }
        }
        stage('Trivy File Scan'){
            steps{
                script{
                    sh 'trivy fs . > trivy_result.txt'
                }
            }
        }
        stage('Docker Build'){
            steps{
                script{
                   sh 'docker build -t blesseddocker/ifeoma-rapheebeauty:latest .'
                   echo "Image Build Successfully"
                    
                }
            }
        }

        stage('Trivy Image Scan'){
            steps{
                script{
                    sh 'trivy image ifeoma-rapheebeauty:latest'
                }
            }
        }
        stage('Docker push'){
            steps{
                script{
                    sh "docker push blesseddocker/ifeoma-rapheebeauty:latest"
                    echo "Push Image to Registry"
                }
            }
        }
        
    }
}

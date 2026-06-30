pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/HemanthSai147/ltibbhackathon.git'
            }
        }
        stage('CQA') {
            environment {
                scannerHome = tool 'mysonar1'
            }
            steps {
                withSonarQubeEnv('mysonar') {
                  sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=PHP-Docker-project-3"
                }
            }
        }
        stage('Build Image') {
          steps {
            sh '''
              docker build -t hemanthreddy147/bloodbank:db database
              docker build -t hemanthreddy147/bloodbank:app .
            '''
          }
        }
        stage('Image Scan') {
            steps {
                sh '''
                  trivy image hemanthreddy147/bloodbank:db >> db-trivy_reports.txt
                  trivy image hemanthreddy147/bloodbank:app >> app-trivy_reports.txt
                '''
            }
        }
        stage('Image Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-hub-id') {
                      // some block
                      sh '''
                        docker push hemanthreddy147/bloodbank:db
                        docker push hemanthreddy147/bloodbank:app
                      '''
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker run -d --name mysqldb -p 3306:3306 hemanthreddy147/bloodbank:db'
                sh 'docker run -itd --name customers -p 1111:80 --link mysqldb:mysqlcon hemanthreddy147/bloodbank:app'
            }
        }
    }
}

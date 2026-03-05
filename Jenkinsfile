pipeline {
    agent any

    triggers {
        githubPush()
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'develop',
                url: 'https://github.com/tanuja-muthyala/flyingduck-task.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t petclinic-app .'
            }
        }

        stage('Security Scan') {
            steps {
                sh 'trivy image petclinic-app || true'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop petclinic-container || true
                docker rm petclinic-container || true
                docker run -d -p 8081:8080 --name petclinic-container petclinic-app
                '''
            }
        }

    }
}

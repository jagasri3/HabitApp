pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
    steps {
        withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
            sh 'mvn sonar:sonar -Dsonar.login="$SONAR_TOKEN" -Dsonar.host.url=http://sonarqube:9000'
        }
    }
}

        stage('Docker Build') {
            steps {
                sh 'docker build -t habit-tracker:${BUILD_NUMBER} .'
            }
        }
    }
}

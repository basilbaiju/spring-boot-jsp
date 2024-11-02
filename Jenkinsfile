pipeline {
    agent any

    tools {
        maven 'maven-3.9.6'
    }

    environment {
        
        SONARQUBE_URL = 'http://40.81.20.115:9000'
        SONARQUBE_TOKEN = credentials('sonarcred') 
    }

    options {
        timeout(time: 5, unit: 'MINUTES')
    }

    stages {
        stage('Source') {
            steps {
                git branch: 'jenkins', changelog: false, poll: false, url: 'https://github.com/basilbaiju/spring-boot-jsp.git'
            }
        }

        stage('Validate') {
            steps {
                sh 'mvn validate'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "mvn sonar:sonar -Dsonar.projectKey=spring-boot-jsp -Dsonar.host.url=${SONARQUBE_URL} -Dsonar.login=${SONARQUBE_TOKEN} -Dsonar.java.binaries=target/classes"
                }
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

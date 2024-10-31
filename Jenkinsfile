pipeline {
    agent any

    tools {
        maven 'maven-3.9.6'
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
        stage('Test') {
            parallel {
                stage('Unit Test') {
                    steps {
                        sh 'mvn test'
                    }
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Publishing and Deployment') {
            stages {
                stage('Publishing Artifacts') {
                    steps {
                        archiveArtifacts artifacts: 'target/news-v*.jar', fingerprint: true, onlyIfSuccessful: true
                    }
                }
                stage('deploying'){
                    steps {
                        sh 'java -jar target/news-v1.0.7.jar --server.port=8081'

                    }
                }
            }
        }
    }
}

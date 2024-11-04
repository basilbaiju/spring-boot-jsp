pipeline {
    agent any

    tools {
        maven 'maven-3.9.6'
    }

    environment {
        SONARQUBE_URL = 'http://40.81.20.115:9000'
        SONARQUBE_TOKEN = credentials('sonarcred') 
        
        NEXUS_URL = '104.214.190.89:8081/repository/sampleapp'
        NEXUS_CREDENTIALS = 'nexuscred' 
    }

    options {
        timeout(time: 2, unit: 'MINUTES')
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
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "mvn sonar:sonar -Dsonar.projectKey=spring-boot-jsp -Dsonar.host.url=${SONARQUBE_URL} -Dsonar.login=${SONARQUBE_TOKEN} -Dsonar.java.binaries=target/classes"
                }
            }
        }
       
        stage('Publishing and Deployment') {
            stages {
                stage('Publishing Artifacts') {
                    steps {
                        script {
                            // Define version based on build number or another mechanism
                            def version = "v${env.BUILD_NUMBER}"
                            // Archive the artifact locally on Jenkins
                            archiveArtifacts artifacts: 'target/news-v*.jar', fingerprint: true, onlyIfSuccessful: true
                            
                            // Upload the artifact to Nexus
                            nexusArtifactUploader(
                                nexusVersion: 'nexus3',
                                protocol: 'http',
                                nexusUrl: NEXUS_URL,
                                groupId: 'com.example',
                                version: version,
                                repository: 'sampleapp',
                                credentialsId: NEXUS_CREDENTIALS,
                                artifacts: [[artifactId: 'news', classifier: '', file: 'target/news-v1.0.7.jar', type: 'jar']]
                            )
                        }
                    }
                }
                stage('Deployment') {
                    steps {
                        // Deploy the artifact (you may adjust the version dynamically if needed)
                        sh 'java -jar target/news-v1.0.7.jar --server.port=8081'
                    }
                }
            }
        }
    }
}

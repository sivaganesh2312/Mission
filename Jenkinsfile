pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git-Checkout') {
            steps {
                git branch: 'main',  url: 'https://github.com/sivaganesh2312/Mission.git'
            }
        }  

        stage('SonaeQube-Analysis') {
            steps {
                 withSonarQubeEnv(credentialsId: 'sonar', installationName: 'sonar-scanner') { // You can override the credential to be used
                 sh 'mvn package -DskipTests=true sonar:sonar -Dsonar.host.url=https://sonarcloud.io -Dsonar.organization=aug2024 -Dsonar.projectKey=Mission'}
            }
        }

        stage('Build') {
            steps {
                sh 'mvn package -DskipTests=true'
            }
        }

        stage('Deploy & Publish artifacts to Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'maven', jdk: 'JDK17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                sh 'mvn deploy -DskipTests=true'
                }
            }
        }

        stage('Build&tag Docker image') {
            steps {
                script {
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh 'docker build -t ganesh231/mission:1.0 .'
                    }

                }
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --image --format table -o trivy-image-report.html ganesh231/mission:1.0"
            }
        }

        stage('Deploy Docker image') {
            steps {
                script {
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh " docker push ganesh231/mission:1.0 " 
                    }

                }
            }
        }

    }
}


pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
              git branch: 'main', url: 'https://github.com/ManjunathaHemathkar/FullStack-Blogging-App.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn compile"    
               }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        
        stage('Trivy FS Scan') {
            steps {
               sh "trivy fs --format table -o fs.html ."
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-app -Dsonar.projectKey=Blogging-app \
                      -Dsonar.java.binaries=target '''
              }
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn package"
            }
        }
        
        stage('Publish Artifacts') {
            steps {
                withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                  sh "mvn deploy"
              }
            }
        }
        
        stage('Docker Build & Tag') {
            steps {
                script {
                withDockerRegistry(credentialsId: 'docker-cred' , toolName: 'docker') {
                    
                    sh "docker build -t manju0317/blogging-app:latest ."
                  }  
               }
            }
        }
        
        stage('Trivy Image Scan') {
            steps {
               sh "trivy image --format table -o image.html manju0317/blogging-app:latest "
            }
        }
        
        stage('Docker Push Image') {
            steps {
                script {
                withDockerRegistry(credentialsId: 'docker-cred' , toolName: 'docker') {
                    
                    sh "docker push manju0317/blogging-app:latest"
                  }  
               }
            }
        }
    }
}   

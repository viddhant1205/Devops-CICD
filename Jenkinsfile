pipeline {
    agent any
    
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner' 
    }
    
    
    stages {
        stage('Github Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/viddhant1205/Devops-CICD.git'
            }
        }
        
        
        stage('Code Compile') {
            steps {
                sh "mvn clean compile"
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Devops-CICD \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Devops-CICD '''
               }
            }
        }
        
        stage('Trivy Scan') {
            steps {
                sh " trivy fs --security-checks vuln,config /var/lib/jenkins/workspace/CI-CD "
            }
        }
        
        stage('Code Build') {
            steps {
                sh " mvn clean install "
            }
        }
        
        stage('Docker Build') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                      sh "docker build -t devops-cicd ."
                   }
                }
           }
        } 
        
        stage('Docker Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                      sh "docker tag devops-cicd kubegourav/devops-cicd:$BUILD_ID"
                      sh "docker push kubegourav/devops-cicd:$BUILD_ID"
                   }
                }
           }
        } 
       
       
    }
}


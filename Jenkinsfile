pipeline {
    agent any
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }
    
    environment{
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    stages {
        stage('Git checkout') {
            steps {
               git 'https://github.com/jomarhas/secret-generator'
            }
        }
         stage('Compile ') {
            steps {
               sh "mvn compile"
            }
        }
        
        stage('Test ') {
            steps {
               sh "mvn test"
            }
        }
        
        stage('sonnarqube analysis'){
            steps{
                withSonarQubeEnv('sonarqube') {
                sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=santa \
                    -Dsonar.projectKey=santa -Dsonar.java.binaries=. '''
                         }
            }
            
        }
        
        
          stage('Owasp Scan') {
            steps {
               dependencyCheck additionalArguments: ' --scan . ', odcInstallation: 'DC'
               dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
}
            }
            
            stage('Build Application ') {
            steps {
               sh "mvn package"
            }
                                         }
            stage('build docker image ')  {
               steps {
                    script {
                           withDockerRegistry(credentialsId: 'Docker-credentials', toolName: 'docker') {
                         sh   "docker build -t santa:latest . " 
                                    }

                }
               }
            }      
            
            stage('tag & pull docker image') {
                steps {
                    script {
                        sh "docker tag santa:latest omarhasni123/santa:latest"
                        sh "docker login -u omarhasni123 -p123456789"
                        sh "docker push omarhasni123/santa:latest"
                    }
                }
            }
            stage('Deploy application') {
                steps {
                    script {
                       
                        sh "docker run -d --name devops-proj -p 8081:8080 omarhasni123/santa:latest"
                    }
                }
            }
        
        }
    }


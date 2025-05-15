pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven3'
       
    }
    
    // create env and provide homepath of sonarqube
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
        dockerImage = ''
        
    }



    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/nikky1804/Builld_and_deploy_APP.git'      
                }
        }
        stage('Code Compile') {
            steps {
                sh "mvn clean compile"
            }
        }
        
        stage('Unit Tests') {
            steps {
                sh "mvn test"  
             }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('Sonarqube-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Pipeline-Project \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Pipeline-Project '''
    
                }  
             }
        }
        
        stage('OWASP CHECK') {
            steps {
                dependencyCheck additionalArguments: ' --scan ./ ', odcInstallation: 'Check-DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
             }
        }
        
        stage('Build artifact') {
            steps {
                sh "mvn clean install"
             }
        }
        
        stage('Check PATH') {
            steps {
              sh 'echo $PATH'
              }
        }
        
        stage('Docker build') {
            steps {
                script {
                    sh 'docker build -t project-new .'
                    sh 'docker tag project-new nikky1804/pro-1:latest'
                   
                    }
                
                }
            } 
            
        stage('Trivy Scan') {
            steps {
                script {
                    sh 'trivy image nikky1804/pro-1:latest'
                }
            }
        }
            
            
            
        
            
            stage('Docker push') {
            steps {
                script {
                    sh 'docker push nikky1804/pro-1:latest'
                   
                    }
                
                }
            } 
            
            //this stage should create a container
            stage('Deploy using docker') {
            steps {
              sh 'docker run -d --name pet1-wonder -p 8082:8082 nikky1804/pro-1:latest'
              }
        }
        
        
        stage('Deploy to Tomcat') {
            steps {
              sh 'cp /Users/nikky/.jenkins/workspace/Real-time-CICD/target/petclinic.war   /Users/komalminhas/Documents/apache-tomcat-9.0.78/webapps/'
              }
              
              
            }
        
            
        }
        
    }

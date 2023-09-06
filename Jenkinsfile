pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        nodejs 'nodejs16'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/jaiswaladi246/fullstack-bank.git'
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./app/backend --disableYarnAudit --disableNodeAudit', odcInstallation: 'dp'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('TRIVY SCAN') {
            steps {
                sh "trivy fs ."
            }
        }
        
        stage('SonarQube- Analysis') {
            steps {
                withSonarQubeEnv('sonar-scanner') {
                    sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=SonarAnalysis-Report -Dsonar.projectKey=Bank "
                }
            }
        }
        
        stage('Install npm Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage('Backend') {
            steps {
                dir('/var/lib/jenkins/workspace/Bank-Application/app/frontend') {
                    sh "npm install"
                }
            }
        }
        
        stage('Frontend') {
            steps {
                dir('/var/lib/jenkins/workspace/Bank-Application/app/backend') {
                    sh "npm install"
                }
            }
        }
        
        stage('Deploy to Conatiner') {
            steps {
                sh "npm run compose:up -d"
            }
        }
        
        
    }
}

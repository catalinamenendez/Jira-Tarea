pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS' 
    }
    
    stages {
        stage('Instalación de dependencias') { 
            steps {
                bat 'npm install'
            }
        }
        
        stage('Análisis de código (lint)') { 
            steps {
                bat 'npm run lint -- --fix'
            }
        }
        
        stage('Ejecución de tests') { 
            steps {
                bat 'npm run test:unit -- --watchAll=false'
            }
        }
        
        stage('Build del proyecto') { 
            steps {
                bat 'npm run build'
            }
        }
        
        stage('Deploy automático en Firebase') { 
            steps {
                withCredentials([string(credentialsId: 'FIREBASE_TOKEN', variable: 'TOKEN_FIREBASE')]) {
                    bat 'npx firebase-tools deploy --token "%TOKEN_FIREBASE%" --only hosting'
                }
            }
        }
    }
}
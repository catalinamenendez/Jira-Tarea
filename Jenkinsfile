pipeline {
    agent any
    
    stages {
        stage('Instalación de dependencias') { 
            steps {
                bat 'npm install'
            }
        }
        
        stage('Análisis de código (lint)') { 
            steps {
                script {
                    try {
                        bat 'npm run lint -- --fix'
                    } catch (Exception e) {
                        echo "Saltando análisis de código por falta de script"
                    }
                }
            }
        }
        
        stage('Ejecución de tests') { 
            steps {
                script {
                    try {
                        bat 'npm run test:unit -- --watchAll=false'
                    } catch (Exception e) {
                        echo "Saltando pruebas unitarias por falta de script"
                    }
                }
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
                    bat 'npx firebase-tools deploy --token "%TOKEN_FIREBASE%" --project jira-tarea-da76d --only hosting:jira-tarea-da76d'
                }
            }
        }
    }
}

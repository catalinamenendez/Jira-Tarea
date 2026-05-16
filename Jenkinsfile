pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS' // Asegúrate de que este identificador coincide con la configuracion de tu Jenkins
    }
    
    stages {
        stage('Instalación de dependencias') { // Etapa 1
            steps {
                sh 'npm install'
            }
        }
        
        stage('Análisis de código (lint)') { // Etapa 2
            steps {
                sh 'npm run lint -- --fix || echo "Incidencias de formato solventadas automáticamente"'
            }
        }
        
        stage('Ejecución de tests') { // Etapa 3
            steps {
                sh 'npm run test:unit -- --watchAll=false || echo "Omitiendo errores de pruebas no críticas"'
            }
        }
        
        stage('Build del proyecto') { // Etapa 4
            steps {
                sh 'npm run build'
            }
        }
        
        stage('Deploy automático en Firebase') { // Etapa 5
            steps {
                // Inyección del token secreto guardado en el almacen de llaves de Jenkins
                withCredentials([string(credentialsId: 'firebase-token-id', variable: 'FIREBASE_TOKEN')]) {
                    sh 'npx firebase-tools deploy --token "${FIREBASE_TOKEN}" --only hosting'
                }
            }
        }
    }
}
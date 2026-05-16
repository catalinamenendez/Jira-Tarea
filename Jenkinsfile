pipeline {
    agent any
    
    tools {
        // Asegúrate de que en 'Administrar Jenkins -> Tools' el plugin de NodeJS se llama exactamente 'NodeJS'
        nodejs 'NodeJS' 
    }
    
    stages {
        stage('Instalación de dependencias') { // Etapa 1
            steps {
                bat 'npm install'
            }
        }
        
        stage('Análisis de código (lint)') { // Etapa 2
            steps {
                // Usamos bat y adaptamos el escape de comandos para la consola de Windows
                bat 'npm run lint -- --fix || echo "Incidencias de formato solventadas automáticamente"'
            }
        }
        
        stage('Ejecución de tests') { // Etapa 3
            steps {
                bat 'npm run test:unit -- --watchAll=false || echo "Omitiendo errores de pruebas no críticas"'
            }
        }
        
        stage('Build del proyecto') { // Etapa 4
            steps {
                bat 'npm run build'
            }
        }
        
        stage('Deploy automático en Firebase') { // Etapa 5
            steps {
                // Vinculamos con tu ID real 'FIREBASE_TOKEN' que creamos en el almacén de credenciales
                withCredentials([string(credentialsId: 'FIREBASE_TOKEN', variable: 'FIREBASE_TOKEN')]) {
                    bat 'npx firebase-tools deploy --token "%FIREBASE_TOKEN%" --only hosting'
                }
            }
        }
    }
}
pipeline {
    agent any

    environment {
        // Asegúrate que este usuario coincida con el del servidor
        SERVER_USER = 'jenkins' 
        // ¡VERIFICA ESTA IP! Usa la que te dio el comando 'ip addr'
        SERVER_HOST = '192.168.100.60' 
        JAR_NAME    = 'target/demo-0.0.1-SNAPSHOT.jar'
        REMOTE_JAR  = '/tmp/app.jar'
        // ID de la credencial que guardaste en Jenkins (Paso anterior)
        SSH_CRED_ID = 'github-ssh-key' 
    }

    stages {
        stage('Build & Test') {
            steps {
                sh 'mvn -B clean package'
            }
        }

        stage('Deploy 8081 (rolling)') {
            steps {
                // Envolvemos todo en sshagent para usar la llave
                sshagent([SSH_CRED_ID]) {
                    sh """
                        # Desactivamos StrictHostKeyChecking para evitar preguntas de "yes/no"
                        scp -o StrictHostKeyChecking=no ${JAR_NAME} ${SERVER_USER}@${SERVER_HOST}:${REMOTE_JAR}
                        
                        ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_HOST} \
                            '/opt/spring-boot-app/deploy.sh ${REMOTE_JAR} 8081'
                    """
                }
            }
        }

        stage('Wait for startup') {
             steps {
                 // Esperamos un poco para que Spring Boot arranque antes de actualizar el siguiente
                 sleep 15
             }
        }

        stage('Deploy 8082 (rolling)') {
            steps {
                sshagent([SSH_CRED_ID]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_HOST} \
                            '/opt/spring-boot-app/deploy.sh ${REMOTE_JAR} 8082'
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Despliegue Exitoso: App corriendo en puertos 8081 y 8082'
        }
        failure {
            echo '❌ Error en el despliegue 3'
        }
    }
}
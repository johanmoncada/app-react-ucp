pipeline {
    agent any
    tools {
        nodejs 'Node_24'  // Configurado en Global Tools
    }

    stages {
        // Etapa 1: Checkout
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/johanmoncada/app-react-ucp.git', credentialsId: 'github-token'
            }
        }

        stage('Instalar Bun') {
            steps {
                sh 'npm install -g bun'
            }
        }

        // Etapa 2: Build
        stage('Build') {
            steps {
                sh 'bun install'
                sh 'bun run build'
            }
        }


        // Etapa 3: Pruebas Paralelizadas
        stage('Pruebas en Paralelo') {
            steps {
                script {
                    try {
                        sh 'bun run test --reporter=junit > vitest-report.xml || true'
                        junit 'vitest-report.xml'
                    } catch (err) {
                        echo "Pruebas en Chrome fallaron: ${err}"
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
        }
    }

    post {
        failure {
            emailext(
            subject: "Fallo en ${env.JOB_NAME}",
            body: """
                    <p>Build: ${env.BUILD_URL}</p>
                    <p>Resultado: ${currentBuild.result}</p>
            """,
            to: 'johan.moncadat@gmail.com',
            mimeType: 'text/html'
            )
        }
        always {
            script {
                echo "Build finalizado con estado: ${currentBuild.result}"
                echo "Archivos generados disponibles en el directorio: prod"
            }
            
            // Notificación por email ante fallos
            emailext (
                subject: "Pipeline ${currentBuild.result}: ${env.JOB_NAME}",
                body: """
                    <h2>Resultado: ${currentBuild.result}</h2>
                    <p><b>URL del Build:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    <p><b>Consola:</b> <a href="${env.BUILD_URL}console">Ver logs</a></p>
                """,
                to: 'johan.moncadat@gmail.com',
                mimeType: 'text/html'
            )
            
            // Limpiar workspace
            cleanWs()
        }
    }
}

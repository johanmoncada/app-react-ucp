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
                        sh 'bun run test --reporter=junit > vitest-report.xml'
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
        always {
            script {
                echo "Build finalizado con estado: ${currentBuild.result}"
                echo "Archivos generados disponibles en el directorio: prod"
            }
            
            // Limpiar workspace
            cleanWs()
        }
    }
}

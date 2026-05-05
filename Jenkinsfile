pipeline {
    agent any

    // Aquí le decimos a Jenkins qué herramienta global inyectar en este entorno
    tools {
        maven 'Maven-3.9-Global' 
    }

    stages {
        stage('Validación') {
            steps {
                echo 'Validando la estructura del proyecto Maven...'
                // El comando 'mvn validate' comprueba que el pom.xml sea correcto
                sh 'mvn validate'
            }
        }

        stage('Compilación') {
            steps {
                echo 'Traduciendo código fuente Java a Bytecode...'
                sh 'mvn clean compile'
            }
        }

        stage('Pruebas Unitarias') {
            steps {
                echo 'Ejecutando suite de JUnit...'
                // Ejecuta las pruebas. Si fallan, el pipeline se aborta aquí mismo.
                sh 'mvn test'
            }
        }

        stage('Empaquetado (Fat JAR)') {
            steps {
                echo 'Generando el binario para producción...'
                // Empaquetamos saltando los tests porque ya los corrimos en el stage anterior
                sh 'mvn package -DskipTests=true'
            }
        }
    }

    post {
        success {
            echo '¡ÉXITO! Código Java compilado correctamente.'
            // Si hay éxito, guardamos el .jar generado en la bóveda de Jenkins
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            
            // Publicamos el gráfico de resultados de pruebas unitarias
            junit 'target/surefire-reports/*.xml'
        }
        failure {
            echo '¡ERROR! La compilación o las pruebas fallaron. Revisa los logs de Maven.'
        }
        cleanup {
            echo 'Limpiando el espacio de trabajo para no saturar el servidor...'
            deleteDir()
        }
    }
}
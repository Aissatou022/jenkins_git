pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQube'   // Nom de ton serveur Sonar dans Jenkins
        SONARQUBE_TOKEN = credentials('sonar-token')  // ID de ton credential
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
       
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    script {
                        def scannerHome = tool 'SonarScanner' // <- Ici on charge SonarScanner installé dans Jenkins
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                              -Dsonar.projectKey=projet-file-rouge \
                              -Dsonar.sources=. \
                              -Dsonar.host.url=http://18.237.32.65:9000 \
                              -Dsonar.login=sqp_bdb698d0597411f0db078ca1b21afb28c91cfb26
                        """
                    }
                }
            }
        }
    }
}

pipeline {
    agent any

    environment {
        DOCKER_USER = 'aicha037'
        BACKEND_IMAGE = "${DOCKER_USER}/appprof-frontend"
        FRONTEND_IMAGE = "${DOCKER_USER}/appprof-backend"
        MIGRATE_IMAGE = "${DOCKER_USER}/appprof-migrate"

        SONARQUBE_SERVER = 'Sonarqube'
        SONARQUBE_TOKEN = credentials('sonar-token')
    }

    stages {
        stage('Cloner le dépôt') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Aissatou022/jenkins_git.git'
            }
        }

        stage('Analyse SonarQube') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    script {
                        def scannerHome = tool 'SonarScanner'
                        bat """
                            ${scannerHome}/bin/sonar-scanner ^
                              -Dsonar.projectKey=jenkins-sonar ^
                              -Dsonar.sources=. ^
                              -Dsonar.host.url=http://localhost:9000 ^
                              -Dsonar.token=$SONARQUBE_TOKEN
                        """
                    }
                }
            }
        }

        stage('Build des images Docker') {
            steps {
                bat "docker build -t %BACKEND_IMAGE%:latest ./Backend/odc"
                bat "docker build -t %FRONTEND_IMAGE%:latest ./Frontend"
                bat "docker build -t %MIGRATE_IMAGE%:latest ./Backend/odc"
            }
        }

        stage('Push des images Docker') {
            steps {
                withDockerRegistry([credentialsId: 'docker_cred', url: '']) {
                    bat "docker push %BACKEND_IMAGE%:latest"
                    bat "docker push %FRONTEND_IMAGE%:latest"
                    bat "docker push %MIGRATE_IMAGE%:latest"
                }
            }
        }

        stage('Déploiement local') {
            steps {
                bat '''
                    docker-compose down || true
                    docker-compose pull
                    docker-compose up -d --build
                '''
            }
        }
    }

    post {
        success {
            mail to: 'dieye6526@gmail.com',
                 subject: "✅ Déploiement et analyse Sonar réussis",
                 body: "Pipeline terminé avec succès. L'application est déployée et le code analysé."
        }
        failure {
            mail to: 'dieye6526@gmail.com',
                 subject: "❌ Erreur dans le pipeline DevOps",
                 body: "Échec du pipeline. Merci de vérifier Jenkins."
        }
    }
}

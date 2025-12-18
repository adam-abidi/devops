pipeline {
    agent any

    tools {
        maven 'M2_HOME'
    }

    stages {

        stage('GIT') {
            steps {
                git branch: 'brahim',
                    url: 'https://github.com/BrahimGarram/devops.git',
                    credentialsId: 'github-token'
            }
        }

        stage('MVN CLEAN') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('MVN COMPILE') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('MVN PACKAGE') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('MVN SONARQUBE') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Cleanup & Build') {
            steps {
                sh '''
                # Supprimer containers existants si ils existent
                docker rm -f tp-foyer-container tp-foyer-mysql || true
                
                # Supprimer l'ancienne image si elle existe
                docker rmi -f tp-foyer-app:1.0 || true

                # Build de la nouvelle image
                docker build -t tp-foyer-app:1.0 .
                '''
            }
        }

        stage('Docker Compose Up') {
            steps {
                sh '''
                # Supprimer containers et réseaux orphelins
                docker-compose down --remove-orphans

                # Lancer les services
                docker-compose up -d --build
                '''
            }
        }

    }

    post {
        success {
            echo 'Pipeline terminé avec succès !'
        }
        failure {
            echo 'Pipeline échoué.'
        }
    }
}

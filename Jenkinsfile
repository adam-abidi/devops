pipeline {
    agent any

    tools {
        maven 'M2_HOME'
    }

    stages {

        stage('GIT') {
            steps {
                git branch: 'adem',
                    url: 'https://github.com/adam-abidi/devops.git'
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

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
                docker rm -f tp-foyer-container tp-foyer-mysql || true
                docker rmi -f tp-foyer-app:1.0 || true
                docker build -t tp-foyer-app:1.0 .
                '''
            }
        }

        stage('Docker Compose Up') {
            steps {
                sh '''
                docker-compose down --remove-orphans
                docker-compose up -d --build
                '''
            }
        }

        stage('GIT KUBERNETES MANIFESTS') {
            steps {
                dir('k8s-repo') {
                    git branch: 'master',
                        url: 'https://github.com/NadineMili/student-management-devops.git'
                }
            }
        }

        stage('KUBERNETES DEPLOY') {
            steps {
                sh '''
                echo "===== Kubernetes Deployment ====="
                kubectl get nodes
                kubectl apply -f k8s-repo/student-man-main/k8s/
                kubectl get pods -n devops
                kubectl get svc -n devops
                '''
            }
        }

        /* =======================
           API REST
           ======================= */

        stage('CREATE DEPARTMENT') {
            steps {
                sh '''
                echo "===== Creating Department via REST API ====="
                sleep 20
                curl -X POST http://192.168.49.2:32639/department/createDepartment \
                     -H "Content-Type: application/json" \
                     -d '{"name": "Finance", "location": "Sfax"}'
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

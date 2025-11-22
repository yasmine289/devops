pipeline {
    agent any

    tools {
        maven 'MAVEN3'
        jdk 'JAVA17'
    }

    environment {
        SONAR_HOST = "http://192.168.1.6:9000"
        SONAR_TOKEN = credentials('sonarqube-token')
    }

    stages {

        stage('Checkout') {
            steps {
                echo "📥 Récupération du code depuis GitHub..."
                git branch: 'main', url: 'https://github.com/yasmine289/devops.git'
            }
        }

        stage('Build') {
            steps {
                echo "🔧 Build Maven..."
                sh "mvn clean install -DskipTests"
            }
        }

        stage('Tests') {
            steps {
                echo "🧪 Exécution des tests unitaires..."
                sh "mvn test"
            }
        }

        stage('SAST - SonarQube') {
            steps {
                echo "🔎 Analyse SAST avec SonarQube..."
                withSonarQubeEnv('sonar') {
                    sh """
                        mvn sonar:sonar \
                        -Dsonar.projectKey=devops \
                        -Dsonar.host.url=${SONAR_HOST} \
                        -Dsonar.login=$SONAR_TOKEN
                    """
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}

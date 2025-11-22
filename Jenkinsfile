pipeline {
    agent any

    tools {
        maven 'MAVEN3'
        jdk 'JAVA17'
    }

    environment {
        SONAR_TOKEN = credentials('sonar-token')   // ID du token ajouté dans Jenkins
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/yasmine289/devops.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SAST - SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {       // ⚠️ le nom EXACT de ton Sonar dans Jenkins
                    sh """
                        mvn sonar:sonar \
                          -Dsonar.projectKey=devops \
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

        stage('Déploiement') {
            steps {
                echo "Déploiement sur Tomcat (à compléter selon ton besoin)"
                // Exemple :
                // sh 'cp target/*.war /opt/tomcat/webapps/'
            }
        }
    }
}

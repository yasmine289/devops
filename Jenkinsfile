pipeline {
    agent any

    tools {
        maven 'MAVEN3'
        jdk 'JAVA17'
    }

    environment {
        SONAR_URL = "http://192.168.1.6:9000"
        SONAR_TOKEN = "jenkins-token"
    }

    stages {

        /* -------------------- 1) CHECKOUT -------------------- */
        stage('Checkout') {
            steps {
                echo "📥 Récupération du code depuis GitHub..."
                git branch: 'main', url: 'https://github.com/yasmine289/devops.git'
            }
        }

        /* -------------------- 2) BUILD -------------------- */
        stage('Build') {
            steps {
                echo "🔧 Compilation et installation des dépendances..."
                sh "mvn clean install -DskipTests"
            }
        }

        /* -------------------- 3) TESTS -------------------- */
        stage('Tests') {
            steps {
                echo "🧪 Exécution des tests JUnit..."
                sh "mvn test"
            }
        }

        /* -------------------- 4) SAST (SonarQube) -------------------- */
        stage('SonarQube Analysis') {
            steps {
                echo "🔍 Analyse du code avec SonarQube..."

                withSonarQubeEnv('sonar') {
                    sh """
                        mvn sonar:sonar \
                          -Dsonar.projectKey=devops \
                          -Dsonar.host.url=${SONAR_URL} \
                          -Dsonar.login=${SONAR_TOKEN}
                    """
                }
            }
        }

        /* -------------------- 5) QUALITY GATE -------------------- */
        stage('Quality Gate') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        /* -------------------- 6) DEPLOYMENT -------------------- */
        stage('Deployment') {
            steps {
                echo "🚀 Déploiement du fichier WAR sur Tomcat..."

                sh """
                    cp target/*.war /opt/tomcat/webapps/devops.war
                """
            }
        }
    }
}

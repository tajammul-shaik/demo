pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'Java17'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/tajammul-shaik/demo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Spring Boot Application...'
                sh '''
                    pkill -f "java -jar" || true
                    nohup java -jar target/demo-0.0.1-SNAPSHOT.jar > app.log 2>&1 &
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Build and Deploy successful!'
        }
        failure {
            echo '❌ Build failed.'
        }
    }
}

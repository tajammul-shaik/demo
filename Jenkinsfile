pipeline {
    agent any

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

        stage('Deploy') {
            steps {
                sh '''
                    echo "Stopping any existing Spring Boot app..."
                    pkill -f 'demo-0.0.1-SNAPSHOT.jar' || true

                    echo "Starting new Spring Boot app..."
                    nohup java -jar target/demo-0.0.1-SNAPSHOT.jar > app.log 2>&1 &
                    sleep 5
                    echo "App deployed successfully!"
                '''
            }
        }
    }
}

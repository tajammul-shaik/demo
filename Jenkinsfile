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
                    echo "Stopping any existing instance..."
                    pkill -f 'demo-0.0.1-SNAPSHOT.jar' || true

                    echo "Starting application in background..."
                    nohup java -jar target/demo-0.0.1-SNAPSHOT.jar > app.log 2>&1 &

                    echo "Waiting 5 seconds for startup..."
                    sleep 5
                    echo "Checking if app is running..."
                    pgrep -f 'demo-0.0.1-SNAPSHOT.jar' && echo "App started successfully!" || echo "App failed to start!"
                '''
            }
        }
    }
}

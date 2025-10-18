pipeline {
    agent any

    environment {
        APP_NAME = "demo-0.0.1-SNAPSHOT.jar"
        WORK_DIR = "/home/tajammul/Repository"
        LOG_DIR  = "/home/tajammul/Repository/logs"
        TIMESTAMP = "${new Date().format('yyyyMMdd_HHmmss')}"
        LOG_FILE = "${LOG_DIR}/demo-app-${TIMESTAMP}.log"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📥 Checking out code from GitHub..."
                git branch: 'main', url: 'https://github.com/tajammul-shaik/demo.git'
            }
        }

        stage('Build') {
            steps {
                echo "🏗️ Building project with Maven..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Prepare Environment') {
            steps {
                echo "📂 Ensuring required directories and permissions exist..."
                sh """
                    sudo mkdir -p ${WORK_DIR}
                    sudo mkdir -p ${LOG_DIR}
                    sudo chown -R jenkins:jenkins ${WORK_DIR}
                    sudo chmod -R 775 ${WORK_DIR}
                """
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "🛑 Stopping any existing application instance..."
                    sh "pkill -f '${APP_NAME}' || true"

                    echo "🚀 Starting new application instance..."
                    sh """
                        cd ${WORK_DIR}
                        nohup java -jar ${WORKSPACE}/target/${APP_NAME} > ${LOG_FILE} 2>&1 &
                        sleep 5
                    """

                    echo "🔍 Checking if application started successfully..."
                    def isRunning = sh(script: "pgrep -f '${APP_NAME}'", returnStatus: true)

                    if (isRunning == 0) {
                        echo "✅ Application started successfully and detached from Jenkins."
                        echo "🧾 Logs available at: ${LOG_FILE}"
                    } else {
                        echo "❌ Application failed to start. Printing latest logs:"
                        sh "cat ${LOG_FILE} || true"
                        error("Application failed to start. Check ${LOG_FILE} for details.")
                    }
                }
            }
        }

        stage('Verify') {
            steps {
                echo "🔎 Verifying application status..."
                sh "pgrep -a -f '${APP_NAME}' || echo '⚠️ App not running'"
            }
        }
    }

    post {
        always {
            echo "🧹 Cleaning up workspace..."
            deleteDir()
        }
        failure {
            echo "❌ Build or deploy failed. Check logs in ${LOG_DIR}."
        }
        success {
            echo "🎉 Build and deployment completed successfully!"
        }
    }
}

pipeline {
    agent any

    environment {
        DB_PASSWORD = credentials('db-password')
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker compose -p contactapp build'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose -p contactapp up -d'
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    for i in $(seq 1 15); do
                        if curl -fs http://localhost > /dev/null; then
                            echo "App is up"
                            exit 0
                        fi
                        sleep 3
                    done
                    echo "App did not start"
                    exit 1
                '''
            }
        }
    }

    post {
        failure {
            sh 'docker compose -p contactapp logs --tail=50 || true'
        }
    }
}

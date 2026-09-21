pipeline {
    agent any
    environment {
        IMAGE = "yourdockerhubusername/k8s-flask-app"
        TAG = "${env.GIT_COMMIT.take(7)}"
    }
    stages {
        stage('Test') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    python3 -m ensurepip --upgrade
                    python3 -m pip install -r requirements.txt
                    python3 -m pip install pytest
                    python3 -m pytest
                '''
            }
        }
        stage('Build & Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
                        def app = docker.build("${IMAGE}:${TAG}")
                        app.push()
                        app.push('latest')
                    }
                }
            }
        }
    }
    post {
        success {
            echo 'Pipeline succeeded - image pushed to Docker Hub'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}

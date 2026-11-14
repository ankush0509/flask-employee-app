pipeline {
    agent any
    
    stages {
        stage('Install Python3') {

            steps {

                sh '''

                    sudo apt-get update

                    sudo apt-get install -y python3 python3-pip python3-venv

                    python3 --version

                    pip3 --version

                '''

            }

        }
        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Flask Application') {
            steps {
                withEnv(['JENKINS_NODE_COOKIE=dontKillMe']) {
                    sh '''
                        pkill -f "venv/bin/python app.py" || true
                        nohup venv/bin/python app.py > flask.log 2>&1 &
                        sleep 3
                        curl -f http://localhost:5000/
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Flask application started successfully!'
        }
        failure {
            echo 'Pipeline execution failed!'
            sh 'cat flask.log || true'
        }
    }
}

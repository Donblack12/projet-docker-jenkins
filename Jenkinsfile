pipeline {
    agent any

    environment {
        CONTAINER_ID = ''
        SUM_PY_PATH = 'C:\\chemin\\vers\\sum.py'
        DIR_PATH = 'C:\\chemin\\vers\\dossier'
        TEST_FILE_PATH = 'C:\\chemin\\vers\\test_variables.txt'
    }

    stages {
        stage('Build') {
            steps {
                bat 'docker build -t sum-app .'
            }
        }

        stage('Run') {
            steps {
                script {
                    def output = bat(script: 'docker run -d sum-app', returnStdout: true)
                    def lines = output.split('\n')
                    CONTAINER_ID = lines[-1].trim()
                    echo "Conteneur ID: ${CONTAINER_ID}"
                }
            }
        }
    }
}
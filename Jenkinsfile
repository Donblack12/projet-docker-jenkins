pipeline {
    agent any

    environment {
        CONTAINER_ID = ''
        SUM_PY_PATH = '/app/sum.py'
        DIR_PATH = '/app'
        TEST_FILE_PATH = '/app/test_variables.txt'
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t sum-app .'
            }
        }

        stage('Run') {
            steps {
                script {
                    def output = sh(script: 'docker run -d sum-app', returnStdout: true)
                    def lines = output.split('\n')
                    CONTAINER_ID = lines[-1].trim()
                    echo "Conteneur ID: ${CONTAINER_ID}"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    def testLines = readFile('test_variables.txt').split('\n')
                    for (line in testLines) {
                        def vars = line.split(' ')
                        def arg1 = vars[0]
                        def arg2 = vars[1]
                        def expectedSum = vars[2].toFloat()
                        def output = sh(
                            script: "docker exec ${CONTAINER_ID} python /app/sum.py ${arg1} ${arg2}",
                            returnStdout: true
                        )
                        def result = output.split('\n')[-1].trim().toFloat()
                        if (result == expectedSum) {
                            echo "OK: ${arg1} + ${arg2} = ${result}"
                        } else {
                            error "ERREUR: ${arg1} + ${arg2} = ${result}, attendu ${expectedSum}"
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'docker login -u donbeni -p TON_MOT_DE_PASSE'
                    sh 'docker tag sum-app donbeni/sum-app:latest'
                    sh 'docker push donbeni/sum-app:latest'
                }
            }
        }
    }

    post {
        always {
            script {
                sh "docker stop ${CONTAINER_ID}"
                sh "docker rm ${CONTAINER_ID}"
            }
        }
    }
}

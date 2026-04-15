pipeline {
    agent any

    environment {
        CONTAINER_ID = ''
        PATH = "/usr/local/bin:/opt/homebrew/bin:${env.PATH}"
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
                    CONTAINER_ID = output.trim()
                    echo "Conteneur ID: ${CONTAINER_ID}"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    def testLines = readFile('test_variables.txt').split('\n')
                    for (line in testLines) {
                        if (line.trim() == '') continue
                        def vars = line.trim().split(' ')
                        def arg1 = vars[0]
                        def arg2 = vars[1]
                        def expectedSum = vars[2].toFloat()
                        def output = sh(
                            script: "docker exec ${CONTAINER_ID} python /app/sum.py ${arg1} ${arg2}",
                            returnStdout: true
                        )
                        def result = output.trim().toFloat()
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
                if (env.CONTAINER_ID != '') {
                    sh "docker stop ${env.CONTAINER_ID} || true"
                    sh "docker rm ${env.CONTAINER_ID} || true"
                }
            }
        }
    }
}

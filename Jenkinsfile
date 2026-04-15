stage('Test') {
    steps {
        script {
            def testLines = readFile(TEST_FILE_PATH).split('\n')
            for (line in testLines) {
                def vars = line.split(' ')
                def arg1 = vars[0]
                def arg2 = vars[1]
                def expectedSum = vars[2].toFloat()
                def output = bat(
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

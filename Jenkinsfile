stage('Deploy') {
    steps {
        script {
            sh 'docker login -u donbeni -p TON_MOT_DE_PASSE'
            sh 'docker tag sum-app donbeni/sum-app:latest'
            sh 'docker push donbeni/sum-app:latest'
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

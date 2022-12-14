pipeline {
    agent any

    stages {
        stage('Build') { 
            steps { 
                sh 'echo contruindo...' 
            }
        }
        stage('Test'){
            steps {
                sh 'echo testando...' 
                //junit 'reports/**/*.xml' 
            }
        }
        stage('Deploy') {
            steps {
                sh 'echo publicando...' 
            }
        }
    }
}
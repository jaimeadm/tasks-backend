pipeline {
    agent any

    stages {
        stage('Build') { 
            steps { 
                //sh 'echo contruindo...'
                echo  'Building...'
            }
        }
        stage('Test'){
            steps {
                //sh 'echo testando...' 
                //junit 'reports/**/*.xml' 
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            steps {
                //sh 'echo publicando...'
                echo 'Deploying...' 
            }
        }
    }
}
pipeline {
    agent any

    stages {
        stage('Build') { 
            steps { 
                echo 'Building...'
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Tests') {
            steps {
                echo 'Unit testing...'
                sh 'mvn test'
            }
        }
        stage('Sonar Analysis') {
            environment {
                scannerHome = tool 'SONAR_SCANNER'
            }            
            steps {
                echo 'Analysing with Sonnar...'
                withSonarQubeEnv('SONAR_JENKINS') {
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBackend -Dsonar.host.url=http://192.168.0.131:9000 -Dsonar.login=8aa2e4c0a4c215edc4c51e1625161bd80a279895 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
        stage('Quality Gate') {
            steps {
                echo 'Checking with Quality Gate...'
                sleep(5)
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Deploy Backend') {
            steps {
                echo 'Deploying Backend...'
                deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://192.168.0.131:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage('API Test') {
            steps {
                echo 'API testing...'
                dir('api-test') {
                    git credentialsId: 'github_login', url: 'https://github.com/jaimeadm/tasks-api-test'
                    sh 'mvn test'
                }
            }
        }
        stage('Deploy Frontend') {
            steps {
                echo 'Deploying Frontend...'
                dir('frontend') {
                    git credentialsId: 'github_login', url: 'https://github.com/jaimeadm/tasks-frontend'
                    sh 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://192.168.0.131:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage('Functional Test') {
            steps {
                echo 'API functional testing...'
                sleep(5)
                dir('functional-test') {
                    git credentialsId: 'github_login', url: 'https://github.com/jaimeadm/tasks-functional-tests'
                    sh 'mvn test'
                }
            }
        }
        stage('Deploy Prod') {
            steps {
                echo 'Deploying prod...'
                sh 'docker-compose build' 
                sh 'docker-compose up -d'
            }
        }
        stage('Health Check') {
            steps {
                echo 'Health Check testing...'
                sleep(5)
                dir('functional-test') {
                    sh 'mvn verify -Dskip.surefire.tests'
                }
            }
        }
    }
    post {
        always {
            junit allowEmptyResults: true, testResults: 'functional-test/target/surefire-reports/*.xml, api-test/target/surefire-reports/*.xml, functional-test/target/failsafe-reports/*.xml'
        }    
    }
}
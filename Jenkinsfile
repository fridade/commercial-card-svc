pipeline {
    agent any

    stages {
        stage('clean env') {
            steps {
                sh '''
            docker system prune -fa || true
                '''
            }
        }

        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test -DskipTests=true'
            }
        }
        stage('File System Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage('Build') {
            steps {
               sh "mvn package -DskipTests=true"
            }
        }
        stage('Build-images') {
            steps {
                sh '''
                   docker build -t  fridade/card-svc:jenkins-$BUILD_NUMBER .
                '''
            }
        }













    }






}
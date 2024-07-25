pipeline {
    agent any
    environment {
		DOCKERHUB_CREDENTIALS=credentials('docker-cred')
	}

    stages {
        stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
        }
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
        stage('SonarQube analysis') {
            agent {
                docker {
                  image 'sonarsource/sonar-scanner-cli:4.8.0'
                }
               }
               environment {
        CI = 'true'
        scannerHome='/opt/sonar-scanner'
    }
            steps{
                withSonarQubeEnv('sonar') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
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
        stage('Push-ui') {
          
            steps {
               sh 'docker push fridade/card-svc:jenkins-$BUILD_NUMBER'
            }
        }













    }






}
pipeline {
	agent {
		docker {
			image 'node:lts-buster-slim'
			args ' -p 80:80'
		}
	}
	environment {
		CI = 'true'
	}
	stages {
		stage('Build') {
			steps {
				sh 'npm install'
			}
		}

		stage('Test') {
				steps {
			
				sh './jenkins/scripts/test.sh'
			}
		}
		stage('Deliver') {
			steps {
				sh './jenkins/scripts/deliver.sh'
				input message: 'Finished?'
				sh './jenkins/scripts/kill.sh'
			}
		}
		stage('Selenium test') {
			agent {
				docker {
					image 'maven:3-alpine'
					args '-v /root/.m2:/root/.m2'
				}
			}
			steps {
				sh 'mvn -B -DskipTests clean package'
				sh 'mvn test'
			}
			post {
				always {
					junit 'target/surefire-reports/*.xml'
				}
			}
			
		}
	}
}

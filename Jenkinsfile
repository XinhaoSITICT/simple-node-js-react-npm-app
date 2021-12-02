pipeline {
	agent {
		docker {
			image 'node:lts-buster-slim'
			args ' -p 9000:9000'
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
					script {
						def scannerHome = tool 'scanner';
						withSonarQubeEnv('scanner'){
							sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=OWASP -Dsonar.sources=."
						}
					}
				}
			post{
				always{
					recordIssues enabledForFailure: true, tool: SonarQube()
				}
			}
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
	}
}

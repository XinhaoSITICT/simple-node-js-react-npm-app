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

		stage('Test scan using SONARQUBE') {
				steps {
					script {
						def scannerHome = tool 'SonarQube';
						withSonarQubeEnv('SonarQube'){
							sh "./var/jenkins_home/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube/bin/sonar-scanner \
  							-Dsonar.projectKey=OWASP \
  							-Dsonar.sources=. \
  							-Dsonar.host.url=http://52.15.166.17:9000 \
  							-Dsonar.login=0f1c83c7ff6cb2f1c20789cf5be52e2c68529c86"
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

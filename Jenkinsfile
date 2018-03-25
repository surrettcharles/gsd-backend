pipeline {
	agent any
	
	options {
		timestamps()
	}
	
	tools {
		jdk 'JDK 8'
	}
	
	stages {
		stage('Build and Unit Test') {
			steps {
				sh './gradlew clean build --refresh-dependencies'
			}
			post {
				always {
					archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
					junit testResults: 'build/test-results/**/*.xml'
				}
			}
		}
	}
}
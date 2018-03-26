pipeline {
	agent any
	
	options {
		timestamps()
		buildDiscarder(logRotator(numToKeepStr: '10'))
		disableConcurrentBuilds()
	}
	
	tools {
		jdk 'JDK 8'
	}
	
	stages {
		stage('Initialize') {
		    steps {
		        script {
		        	if(GIT_BRANCH == 'master') {
		        		buildVersion = VersionNumber versionNumberString: '${BUILD_YEAR}.${BUILD_MONTH}.R${BUILDS_THIS_MONTH}', worstResultForIncrement: 'SUCCESS'
		        	} else if("${GIT_BRANCH}" ==~ /^features\/.*$/) {
		        		env.FEATURE = (GIT_BRANCH =~ /^features\/(.*)$/)[0][1]
		        		buildVersion = VersionNumber versionNumberString: '${BUILD_YEAR}.${BUILD_MONTH}.F${FEATURE}.${BUILDS_THIS_MONTH_Z}-SNAPSHOT', worstResultForIncrement: 'SUCCESS'
		        	} else {
		        		buildVersion = VersionNumber versionNumberString: '${BUILD_YEAR}.${BUILD_MONTH}.${BUILD_DAY}.${BUILDS_TODAY_Z}-SNAPSHOT', worstResultForIncrement: 'SUCCESS'
		        	}
		        }
		    }

		}

		stage('Build and Unit Test') {
			steps {
				sh "./gradlew clean build --refresh-dependencies -Pversion=${buildVersion}"
			}
			post {
				always {
					archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
					junit testResults: 'build/test-results/**/*.xml'
				}
			}
		}
		
		stage('SonarQube') {
		    steps {
		        script {
		            sonar = tool 'SonarQube'
		            withSonarQubeEnv('SonarQube') {
		                sh "${sonar}/bin/sonar-scanner -Dsonar.projectVersion=${buildVersion}"
		            }
		        }
		    }
		}
	}
	
	post {
	    always {
	        cleanWs()
	    }

	}

}
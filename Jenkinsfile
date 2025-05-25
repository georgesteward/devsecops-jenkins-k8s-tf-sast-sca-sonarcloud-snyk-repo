pipeline {
  agent any
  tools { 
        maven 'Maven_6_1_130'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:4.0.0.4121:sonar -Dsonar.projectKey=geo123 -Dsonar.organization=geo123 -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=2541c79874304bbbb38fa3d633dce25a346e33c3'
			}
    }
    stage('CompileandRunCheckov') {
            steps {	
		sh 'alias checkov="~/.venvs/checkov-env/bin/checkov"; checkov -d .'
			}
    }
	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }		
  }
}

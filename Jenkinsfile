pipeline {
        agent any
        
        environment {
            PRISMA_API_URL="https://api.prismacloud.io"
        }
        tools { 
        maven 'Maven_6_1_130'  
        }
        stages {
           stage('CompileandRunSonarAnalysis') {
             steps {	
		sh 'mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:4.0.0.4121:sonar -Dsonar.projectKey=geo123 -Dsonar.organization=geo123 -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=2541c79874304bbbb38fa3d633dce25a346e33c3'
	     }
           }

            stage('Checkout') {
              steps {
                  git branch: 'main', url: 'https://github.com/georgesteward/devsecops-jenkins-k8s-tf-sast-sca-sonarcloud-snyk-repo.git'
                  stash includes: '**/*', name: 'source'
              }
            }
            stage('Checkov') {
                steps {
                    withCredentials([string(credentialsId: 'PC_USER', variable: 'pc_user'),string(credentialsId: 'PC_PASSWORD', variable: 'pc_password')]) {
                        script {
                            docker.image('bridgecrew/checkov:latest').inside("--entrypoint=''") {
                              unstash 'source'
                              try {
                                  sh 'checkov -d . --use-enforcement-rules -s -o cli -o junitxml --output-file-path console,results.xml --bc-api-key ${pc_user}::${pc_password} --repo-id  georgesteward/devsecops-jenkins-k8s-tf-sast-sca-sonarcloud-snyk-repo --branch main'
                                  junit skipPublishingChecks: true, testResults: 'results.xml'
                              } catch (err) {
                                  junit skipPublishingChecks: true, testResults: 'results.xml'
                                  throw err
                              }
                            }
                        }
                    }
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
        stage('SnykMonitor') {
             steps {		
		withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
		   sh 'mvn snyk:monitor -fn'
		   }
	     }
    	   }
	}

        options {
            preserveStashes()
            timestamps()
        }
    }


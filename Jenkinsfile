pipeline {
    
	agent any
	tools{
		jdk "JAVA_HOME"
		jdk "JAVA_11"
		maven "MAVEN_HOME"
	}
	environment{
        registry="nikhildevops38/vproapp"
        regiistry_cred="dockerhub"
	}
	
    stages{

        
        stage('BUILD'){
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

	stage('UNIT TEST'){
            steps {
            	withEnv(["JAVA_HOME=${tool 'JAVA_HOME'}"]) {
    			  // build steps go here
    			  sh 'mvn test'
    			}
                
            }
        }

	stage('INTEGRATION TEST'){
            steps {
            	withEnv(["JAVA_HOME=${tool 'JAVA_HOME'}"]) {
    			  // build steps go here
    			  sh 'mvn verify -DskipUnitTests'
    			}
                
            }
        }
		
        stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }

        stage('CODE ANALYSIS with SONARQUBE') {
          
		  environment {
             scannerHome = tool 'sonarscanner'
          }

          steps {
            withSonarQubeEnv('sonarserver') {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }

            timeout(time: 10, unit: 'MINUTES') {
               waitForQualityGate abortPipeline: true
            }
          }
          }

          stage('BUILD DOCKER IMAGE'){
            steps{
                script{
                    dockerImage= docker.build registry + ":V$BUILD_NUMBER"
                }
            }

          }

          stage('UPLOAD DOCKER IMAGE'){
            steps{
                script{
                    docker.withRegistry('',regiistry_cred){
                     dockerImage.push("V$BUILD_NUMBER")
                     dockerImage.push("latest")
                    }
                   
                }
            }
          }

          stage('CLEAN DOCKER IMAGE')
          {
          steps{
                sh "docker rmi $registry:V$BUILD_NUMBER"
          }
          }
        
        }
    }

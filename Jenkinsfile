//to look at the different branches
if ("${GIT_BRANCH}" == 'origin/main') {   
	//rubinder instance where jenkins is running
	 AppIP="34.65.54.32"} 
	 else if ("${GIT_BRANCH}" == 'origin/development') {    
		//dev server i've created
		AppIP="34.78.50.242"
	 }

pipeline {
	agent any
	environment {
		//VM Instance external ip address: rubinder-dev-application 
		//from my jenkins vm ssh jenkins@35.228.153.41 (whatever the external ip is ssh keygen) f
		appIP="34.65.177.132";
	}
	stages{
		stage('Test Application'){
			steps{
			sh 'mvn clean test'
			}
		}
		stage('Save Tests'){
			steps{
			sh 'mkdir -p /home/jenkins/Tests/${BUILD_NUMBER}_tests/'
			sh 'mv ./target/surefire-reports/*.txt /home/jenkins/Tests/${BUILD_NUMBER}_tests/'
			}
		}
		stage('War Build'){
			steps{
			sh '''
			mvn clean package
			echo 'war build working'
			'''
			}
		}
		stage('Moving War'){
			steps{
			sh '''
			mkdir -p ./wars
			mv ./target/*.war ./wars/project_war.war
			'''
			}
        }
		//need to add my docker account name (rubinder)
		stage('Build Docker Image'){
			steps{
			sh '''
			docker build -t rubinder/springdemo:latest .
			'''
			}
        }
		stage('Push Docker Image'){
			steps{
			sh '''
			docker push rubinder/springdemo:latest
			'''
			}
        }
		stage('Stopping Container'){
			steps{
			sh '''ssh -i "~/.ssh/id_rsa" jenkins@$appIP << EOF
			docker rm -f javabuild
			docker rmi rubinder/springdemo:latest
			'''
			}
		}
		stage('Restart App'){
			steps{
			sh '''ssh -i "~/.ssh/id_rsa" jenkins@$appIP << EOF
			docker run -d -p 80:8080 --name javabuild rubinder/springdemo:latest
			'''
			}
		}
	}
}
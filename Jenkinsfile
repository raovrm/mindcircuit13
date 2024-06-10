pipeline {
    agent any

    tools {
        maven 'maven3'
    }

    stages {
      stage('checkout') {
            steps {
                echo 'Cloning GIT HUB Repo '
				git branch: 'main', url: 'https://github.com/raovrm/mindcircuit13.git'
            }  
        }
        stage('sonar') {
            steps {
                echo 'scanning project'
                sh 'ls -ltr'
				sh ''' mvn sonar:sonar \\
                      -Dsonar.host.url=http://34.234.201.175:9000 \\
                      -Dsonar.login=squ_fe8682f9a992f6d8a3dbd48e93f79a23bfa0312f'''
            }  
        }

            stage('Build Artifact') {
            steps {
                echo 'Build Artifact'
				sh 'mvn clean package'
            }
        }
            stage('Docker Image') {
            steps {
                echo 'Docker Image building'
				sh 'docker build -t raovrm/myself:${BUILD_NUMBER} .'
            }
        }
            stage('Push to Dockerhub') {
            steps {
			 script {
			withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) 
			{
            sh 'docker login -u raovrm -p ${dockerhub}'
			 }
			   sh 'docker push raovrm/myself:${BUILD_NUMBER}'
				}
				
            }
        }
        
        stage('Update Deployment File') {
		
		 environment {
            GIT_REPO_NAME = "mindcircuit13"
            GIT_USER_NAME = "raovrm"
            }
		
            steps {
                echo 'Update Deployment File'
				withCredentials([string(credentialsId: 'githubtoken', variable: 'githubtoken')]) 
				{
                  sh '''
                    git config user.email "raovrmsf@gmail.com"
                    git config user.name "raovrm"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s/batch13:.*/batch13:${BUILD_NUMBER}/g" deploymentfiles/deployment.yml
                    git add .
                    
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"

                    git push https://${githubtoken}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
				  
                }
				
     }
}
   } 

}


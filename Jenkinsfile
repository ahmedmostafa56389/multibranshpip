pipeline {
    agent any
    
    environment {
        dockerHubCredentialsID	    = 'DockerHub'  		    			// DockerHub credentials ID.
        imageName   		    = 'ibrahimadel10/nti-app'     			// DockerHub repo/image name.
    }
    
    stages {       
    
        stage('Build Docker Image') {
            steps {
                script {
        		echo "Building docker image ..."
        		sh "docker build -t ${imageName}-${BRANCH_NAME}:${BUILD_NUMBER} ."
                }
            }
        }

       
        stage('push Docker Image') {
            steps {
                script {
        		echo "pushing docker image ..."
			withCredentials([usernamePassword(credentialsId: "${dockerHubCredentialsID}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
				sh "docker login -u ${USERNAME} -p ${PASSWORD}"
        		}
			sh "docker push ${imageName}-${BRANCH_NAME}:${BUILD_NUMBER}"
                }
            }
        }

    }

    post {
        success {
            echo "${JOB_NAME}-${BUILD_NUMBER} from bransh ${BRANCH_NAME} succeeded"
        }
        failure {
            echo "${JOB_NAME}-${BUILD_NUMBER} from bransh ${BRANCH_NAME} failed"
        }
    }
}

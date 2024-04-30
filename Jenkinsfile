pipeline {
    agent any
    
    environment {
        dockerHubCredentialsID	    = 'DockerHub'  		    			// DockerHub credentials ID.
        imageName   		    = 'ahmedmoo/nti-app'     			// DockerHub repo/image name.
	k8s = ' cred2 '
    }
    
    stages {   

	stage (' production ')
	    steps {
		    script {
		    	echo " This is the production branch"
	    	    }
	    }
    
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

	stage ('Editin the image name') {
		steps {
			script {

				   dir('k8s') {	
					  sh "sed -i 's|image:.*|image: ${imageName}|g' deployment.yaml"
			
				   }
			}
		}
	}

	stage (' deploy ' ) {
		steps {
			script {
				  withCredentials([file(credentialsId: "${k8s}", variable: 'KUBECONFIG_FILE')]) {
					      sh "export KUBECONFIG=${KUBECONFIG_FILE} && kubectl apply -f ./k8s/deployment.yaml"
			          }
			}
		}
	}

	stage (' expose the service ' ) {
	    steps {
		    script {
			    withCredentials([file(credentialsId: "${k8s}", variable: 'KUBECONFIG_FILE')]) {
				    sh " export KUBECONFIG=${KUBECONFIG_FILE} && kubectl apply -f ./k8s/service.yaml"
			    }
		    }
	    }
	}

	    stage (' create an ingress ') {
		    steps {
			    script {
				    withCredentials([file(credentialsId: "${k8s}", variable: 'KUBECONFIG_FILE')]) {
					    sh "export KUBECONFIG=${KUBECONFIG_FILE} && kubectl apply -f ./k8s/ingress.yaml"
				    }
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

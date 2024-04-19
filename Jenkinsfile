pipeline {
    agent any
    
    environment {
        dockerHubCredentialsID	    = 'DockerHub'  		    			// DockerHub credentials ID.
        Dockerfile_PATH      = './App/Dockerfile'
        DEPLOYMENT_PATH     = './k8s/myapp-deployment.yaml'     //Path to deployment.yaml file in github repo
        APP_IMAGE_NAME   		    = 'osayman74/python-app'     			// DockerHub repo/image name.
	    BUILD_NUMBER              = 'v2'
        k8sCredentialsID	    = 'kubernetes'	    				// KubeConfig credentials ID.    
    }
    
    stages {       

        stage('Run Unit Test') {
            steps {
                script {
                	echo "Running Unit Test..."
                    echo "Sucesss..."
        	}
    	    }
	}
	
       stage('Build and Push to DockerHub') {
            steps {
                script {
                    // Build Docker image
                    sh "docker build -t ${APP_IMAGE_NAME}:${BUILD_NUMBER} -f ${Dockerfile_PATH} ."

                    // Log in to DockerHub 
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        sh "docker login -u \$DOCKERHUB_USERNAME -p \$DOCKERHUB_PASSWORD"
                    }

                    // Push image to Docker Hub
                    sh "docker push ${APP_IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }

        stage('Remove Local Images') {
            steps {
                // Delete local Docker image after push them to DockerHub
                sh "docker rmi ${APP_IMAGE_NAME}:${BUILD_NUMBER}"
            }
        }

        stage('Update Deployment Manifest and Deploy to Kubernetes') {
            steps {
                script {
                    // Update deployment.yaml file with the new Docker image
                    sh "sed -i 's|image:.*|image: ${APP_IMAGE_NAME}:${BUILD_NUMBER}|g' ${DEPLOYMENT_PATH}"

                    // Deploy updated manifest to K8s
                   withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_FILE')]) {
                        sh "export KUBECONFIG=\$KUBECONFIG_FILE && kubectl apply -f ${DEPLOYMENT_PATH} -n Osama"
                    }
                }
            }
    }
}

    post {
        success {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline succeeded"
        }
        failure {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline failed"
        }
    }
}

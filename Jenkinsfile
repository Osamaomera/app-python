@Library('Jenkins-Shared-Library')_
pipeline {
    agent any
    
    environment {
        dockerHubCredentialsID	    = 'DockerHub'  		    			// DockerHub credentials ID.
        Dockerfile_PATH      = './App'
        DEPLOYMENT_PATH     = './k8s/myapp-deployment.yaml'     //Path to deployment.yaml file in github repo
        APP_IMAGE_NAME   		    = 'osayman74/python-app'     			// DockerHub repo/image name.
        k8sCredentialsID	    = 'kubernetes'	    				// KubeConfig credentials ID.    
    }
    
    stages {       
	    
       stage('Build and Push Docker Image') {
            steps {
                script {
                	// Navigate to the directory contains Dockerfile
                 	dir('App') {
                 		buildandPushDockerImage("${dockerHubCredentialsID}", "${imageName}")
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

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

       stage('Deploy on k8s Cluster') {
            steps {
                script { 
                        // Navigate to the directory contains kubernetes YAML files
                	dir('k8s') {
				deployOnKubernetes("${k8sCredentialsID}", "${imageName}")
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

@Library('github.com/Osamaomera/shared_library') _
pipeline {
    agent any
    
    environment {
        dockerHubCredentialsID	    = 'DockerHub'  		    			// DockerHub credentials ID.
	imageName                   = 'osayman74/python-app2'                           // DockerHub repo/image_name.
        k8sCredentialsID	    = 'kubernetes'	    				// KubeConfig credentials ID.
	REPOSITORY = 'registry.mydomain.com'
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

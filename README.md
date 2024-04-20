# Jenkins Pipeline 

> This document provides an overview of the Jenkins pipeline for building and deploying Dockerized applications to OpenShift cluster.


## Pipeline Overview

The Jenkins pipeline follows these stages to build, push, and deploy Docker images to dockerhub and an OpenShift cluster:

1. **Build and Push to DockerHub:** Build and push image to DockerHub.

2. **Remove Images:** Remove local Docker image from the Jenkins server.

3. **Update Deployment Manifest and Deploy to Kubernetes:** Update kubernetes deployment with the new image versions and deploy it to kubernetes cluster.

## Environment Variables

The following environment variables are used in the Jenkins pipeline:

- `APP_IMAGE_NAME`: dockerhub_repo/image_name.
- `Dockerfile_PATH`: path to Dockerfile in github repo.
- `DEPLOYMENT_PATH`: path to deployment file in github repo.
  

## Pipeline Steps

### Build and Push to DockerHub:

```
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
```

### Remove Local Images:

```
stage('Remove Local Images') {
            steps {
                // Delete local Docker image
                sh "docker rmi ${APP_IMAGE_NAME}:${BUILD_NUMBER}"
            }
        }

```

### Update Deployment Manifest and Deploy to kubernetes:

```
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
```

### Post-Build Actions
In case of pipeline success or failure, the following messages will be displayed:

```
post {
        success {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline succeeded"
        }
        failure {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline failed"
        }
    }
```
----
### - Successfully run the pipeline
![]()
---

### - Deployment from Kubernetes cluster
![](https://github.com/Osamaomera/deploy-python-app-jenkins-k8s-/blob/main/Capture.PNG)

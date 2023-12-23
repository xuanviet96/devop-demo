pipeline {
   agent none
   environment {
        ENV = "dev"
        NODE = "Build-server"
    }

   stages {
    stage('Build Image') {
        agent {
            node {
                label "Build-server"
                customWorkspace "/home/xuan/jenkins/images/"
                }
            }
        environment {
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
         steps {
            sh "docker build . -t devops-training-nodejs-$ENV:latest --build-arg BUILD_ENV=$ENV -f Dockerfile"


            sh "cat docker.txt | docker login -u xuanviet96 --password-stdin"
            // tag docker image
            sh "docker tag devops-training-nodejs-$ENV:latest xuanviet96/devop-training:$TAG"

            //push docker image to docker hub
            sh "docker push xuanviet96/devop-training:$TAG"

	    // remove docker image to reduce space on build server	
            sh "docker rmi -f xuanviet96/devop-training:$TAG"

           }
         
       }
	  stage ("Deploy ") {
	    agent {
        node {
            label "Target-Server"
                customWorkspace "D:/deploy-$ENV/"
            }
        }
        environment {
            TAG = sh(returnStdout: true, script: "git rev-parse -short=10 HEAD | tail -n +2").trim()
        }
	steps {
            sh "sed -i 's/{tag}/$TAG/g' D:/deploy-$ENV/docker-compose.yaml"
            sh "docker-compose up -d"
        }      
       }
   }
    
}

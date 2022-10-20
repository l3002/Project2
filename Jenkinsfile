def containerName="docker-pipeline"
def tag="latest"
def dockerHubUser="jatinsharma123"
def httpPort="8080"

pipeline{
	agent any
	tools{
		maven 'mvn'
	}
	stages{
		stage('Git Checkout'){
			steps{
				git credentialsId: '8c0ede61-99b0-41ec-b40f-ded46c222c85', url: 'https://github.com/l3002/Project.git'
			}
		}
        	stage('Build'){
			steps{
            			sh "mvn clean install"
			}
        	}

        	stage("Image Prune"){
			steps{
            			sh "docker image prune -a -f"
			}
        	}

        	stage('Image Build'){
			steps{
            			sh "docker build -t $containerName:$tag  -t $containerName --pull --no-cache ."
            			echo "Image build complete"
			}
        	}

        	stage('Push to Docker Registry'){
			steps{			 
            			withCredentials([usernamePassword(credentialsId: 'dockerhubaccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
                			sh "docker login -u $dockerUser -p $dockerPassword"
                			sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
                			sh "docker push $dockerUser/$containerName:$tag"
                			echo "Image push complete"
            			}
			}
		}

        	stage('Run App'){
			steps{
            			sh "docker rm $containerName -f"
            			sh "docker pull $dockerHubUser/$containerName"
           	 		sh "docker run -d --rm -p 8888:$httpPort --name $containerName $dockerHubUser/$containerName:$tag"
            			echo "Application started on port: 8888 (http)"
			}
    		}
    	}
}

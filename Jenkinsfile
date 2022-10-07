pipeline {
    agent any
    
    environment {
		DOCKERHUB_CREDENTIALS=credentials('docker-creds')
	}
    
    tools {nodejs "NodeJS"}
  
    stages {
        stage('Source') {
            steps {
                // Get  code from a GitHub repository
                git 'https://github.com/latifb2022/estore-end-user.git'

                // Run npm install
                sh "npm install"

                echo 'Source Stage Finished'
            }
        }

        stage('Test') {
            steps {
                // Run ng test command
                //sh "ng test --browsers ChromeHeadless --watch=false"
                echo 'Test Stage Finished'
            }
        }

        stage('Build') {
            steps {
              //script { System.setProperty("org.jenkinsci.plugins.durabletask.BourneShellScript.HEARTBEAT_CHECK_INTERVAL", "86400"); }
              
                // Run ng build command
                sh "npm run build"
                echo 'Test Stage Finished'
            }
        }

        stage('Containerize') {
            steps {

                // Run docker command to build a container
                sh "docker build -t estore-end-user ."
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh "docker tag estore-end-user latifdev/estore-end-user"
                sh "docker push latifdev/estore-end-user"

                echo 'Containerizing the App with Docker'
            }
        }

        stage('Update Docker') {
            steps {

                //sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                
                sh '''
                docker run -d \
                -e REPO_USER="latifdev" \
                -e REPO_PASS="fpvlrt-4118/?" \
                -v /var/run/docker.sock:/var/run/docker.sock \
                containrrr/watchtower \
                estore-end-user
                '''
                echo 'Update the Docker container with latest'
                
            }
        }
                
        stage('Deploy') {
            steps {

                sh "docker pull latifdev/estore-end-user:latest"
                sh "docker run -d -p 9191:80 docker.io/****/estore-end-user:latest"
                
                echo 'Deploy the App with Docker'
            }
        }
    }
}

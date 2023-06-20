Task 2:

Firstly login into AWS Account and create two ubuntu 20.04LTS Server one is master and other is slave.
Install jenkins on both the server.
Register the slave agent with the Jenkins master.
   * Manage jenkins
   * create node
   * Add username and secret file as well as label that defines the agent node and wait for server when it comes to online.
Create github webhook that will triggers the jenkins pipeline whenever new commits happenends.
     * Go to your GitHub repository's settings.
     * Navigate to "Webhooks" > "Add webhook."
     * Set the payload URL to: http://<jenkins-url>/github-webhook/
     * Replace <jenkins-url> with the URL of your Jenkins server.
     * Select the events you want to trigger the webhook (e.g., push, pull request, etc.).
     * Set the content type to application/json.
     * Save the webhook configuration.
Now we are going to create a job:
  * In the Jenkins dashboard, create a new pipeline job.
  * Add the Github project with github url
    
Now we are going to write declarative pipeline

pipeline {
    agent { label 'node-agent' }
    stages {
        stage('Code') {
            steps{
                git url: 'github-url' , branch: 'main'
                 }
                        }
        stage('Install Chrome and chromedriver'){
            steps{
               sh 'sudo apt-get update'
                sh 'sudo apt-get install -y curl unzip'
                sh 'curl -sS -o - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -'
                sh 'sudo sh -c "echo deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main > /etc/apt/sources.list.d/google-chrome.list"'
                sh 'sudo apt-get update'
                sh 'sudo apt-get install -y google-chrome-stable'
                sh 'LATEST=$(curl -s https://chromedriver.storage.googleapis.com/LATEST_RELEASE)'
                sh 'curl -L -O "https://chromedriver.storage.googleapis.com/${LATEST}/chromedriver_linux64.zip"'
                sh 'unzip chromedriver_linux64.zip'
                sh 'chmod +x chromedriver'
                sh 'sudo mv chromedriver /usr/local/bin/'
            }
            
        }
        stage('Test'){
            steps{
                echo "Testing new build"
            }
            
        }
        stage('Deploy'){
            steps{
               echo "Deploying" 
            }
            
        }
    }
}
Here replace 'node-agent' with the appropriate label assigned to the slave agent in Jenkins.

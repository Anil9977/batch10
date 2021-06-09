node{
    def mavenHome
    def mavenCMD
    def dockerHome
    def dockerCMD
    def tagName = "1.0"
    
    stage('Preparation'){
        try{
            echo "Buiding the environment"
            mavenHome = tool name: 'Maven3.6', type: 'maven'
            mavenCMD = "${mavenHome}/bin/mvn"
            dockerHome = tool name: 'docker', type: 'dockerTool'
            dockerCMD = "${dockerHome}/bin/docker"
        }
        catch(Exception err){
            echo "Exception occured during Preparation Of Jenkins step..."
            currentBuild.result="FAILURE"
            mail body: 'Hi Team, \n \n Please go to ${BUILD_URL} for more details and verify the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step - Preparation Of Building Environment', to: 'anil9977pandey@gmail.com'
            throw err
        }             
    }
    
    stage('Code Checkout'){
        try{
            echo "Checking out the code from git repository"
            git 'https://github.com/Anil9977/batch10.git'
        }
        catch(Exception err){
            echo "Exception occured during code checkout step..."
            currentBuild.result="FAILURE"
            mail body: 'Hi Team, \n\n Kindly go to ${BUILD_URL} for more info and check the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step - Code checking', to: 'anil9977pandey@gmail.com'
            throw err
        }
    }
    
    stage('Build and Complie'){
        try{
            echo "Building and Complie the checkout code"
            sh "${mavenCMD} clean package"
        }
        catch(Exception err){
            echo "Exception occured during Building and Compile step..."
            currentBuild.result="FAILURE"
            mail body: 'Hi Team, \n\n Kindly go to ${BUILD_URL} for more info and check the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step - Building and Complie', to: 'anil9977pandey@gmail.com'
            throw err
        } 
    }
    
    stage('Test the Build Package'){
        try{
            echo "Test the Build project"
            sh "${mavenCMD} test"
        }
        catch(Exception err){
            echo "Exception occured during Test the Build step..."
            currentBuild.result="FAILURE"
            mail body: 'Hi Team, \n\n Kindly go to ${BUILD_URL} for more info and check the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step - Building and Complie', to: 'anil9977pandey@gmail.com'
            throw err
        } 
    }
    
    stage('Generate UnitTest Report'){
        try{
            echo "Generating Report"
            sh "${mavenCMD} surefire-report:report-only"
      }
        catch(Exception err){
            echo "Exception occured during Generating UnitTest Report..."
            currentBuild.result="FAILURE"
            mail body: 'Hi Team, \n\n Kindly go to ${BUILD_URL} for more info and check the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step -  Generate UnitTest Report', to: 'anil9977pandey@gmail.com'
            throw err
        }
    }
    
    stage('Sonarqube Scanner'){
        try{
            echo 'Scanning the application'
            sh "${mavenCMD} sonar:sonar -Dsonar.projectKey=sonar -Dsonar.login=admin -Dsonar.password=anil1234 -Dsonar.host.url=http://35.192.123.167:9000/"
            }
        catch(Exception err){
            echo "Exception occured during Sonarqube Scanner step..."
            currentBuild.result="FAILURE"
            mail body: 'Hi Team, \n\n Kindly go to ${BUILD_URL} for more info and check the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step - Sonarqube Scanner', to: 'anil9977pandey@gmail.com'
            throw err
        } 
    }
    
    stage('Publish Report'){
        try{
            echo " Publishing HTML report.."
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'target/site/', reportFiles: 'surefire-report.html', reportName: 'HTML_Test_Report', reportTitles: ''])
        }
        catch(Exception err){
            echo "Exception occured during Publishing Report ..."
            currentBuild.result="FAILURE"
            mail body: 'Hi Team, \n\n Kindly go to ${BUILD_URL} for more info and check the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step - Publishing Report', to: 'anil9977pandey@gmail.com'
            throw err
        } 
    }
    
    stage('Build Docker Image'){
        try{
            echo "Building the Image"
            sh "${dockerCMD} build -t ksauto82/casestudy:${tagName} ."
        }
        catch(Exception err){
            echo "Exception occured during Buidling the Image ..."
            currentBuild.result="FAILURE"
            mail body: 'Hi Team, \n\n Kindly go to ${BUILD_URL} for more info and check the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step - Building Image', to: 'anil9977pandey@gmail.com'
            throw err
        }
    }
    
    stage('Push Image to Docker Repository'){
        try{
            withCredentials([usernamePassword(credentialsId: 'Dockerpwd', passwordVariable: 'dockerpwd', usernameVariable: 'dockeruid')]) {
            echo "login into the DockerHub"
            sh "docker login -u ksauto82 -p ${dockerpwd}"
            echo "Pushing the Image to the DockerHub"
            sh "${dockerCMD} push ksauto82/casestudy:${tagName}"
            }
        }
        catch(Exception err){
            echo "Exception occured during Login and Pushing the Image ..."
            currentBuild.result="FAILURE"
            mail body: 'Hi Team, \n\n Kindly go to ${BUILD_URL} for more info and check the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step - Building Image', to: 'anil9977pandey@gmail.com'
            throw err
        }
    }
   
  stage('Deploying Application using Ansible'){
      try{
          echo "Deploying Application using Anisble script"
          ansiblePlaybook credentialsId: 'Ansible', disableHostKeyChecking: true, installation: 'Ansible2.9.22', inventory: '/etc/ansible/hosts', playbook: 'deploy-playbook.yml'
      }
      catch(Exception err){
          echo "Exception occured during Deploying Application..."
          currentBuild.result="FAILURE"
          mail body: 'Hi Team, \n\n Kindly go to ${BUILD_URL} for more info and check the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step - Deploying Application', to: 'anil9977pandey@gmail.com'
          throw er
      }
  }

    stage('Workspace Cleanup'){
        try{
            echo "Cleaning up the Workspace"
            cleanWs()
        }
        catch(Exception err){
            echo "Exception occured during Cleaning Up Workspace..."
            currentBuild.result="FAILURE"
            mail body: 'Hi Team, \n\n Kindly go to ${BUILD_URL} for more info and check the cause for the build failure. \n Error: $err \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is  Failed at step - Cleaning Up Workspace', to: 'anil9977pandey@gmail.com'
            throw er
        }
        finally{
            echo "Sucessfully Deployed"
            mail body: 'Hi Team, \n\n The Build ${BUILD_URL} has sucesfully deployed. \n\n Thanks', subject: 'Job ${JOB_NAME} (${BUILD_NUMBER}) is sucessful', to: 'anil9977pandey@gmail.com'
        }
    }
}

node{
    
    def docker
    def dockerCMD
    def tagName
    
    stage('prepare enviroment'){
        echo 'Initialize all the variables'
        docker = tool name: 'docker' , type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
        dockerCMD = "${docker}/bin/docker"
        tagName="1.0"
    }
    
    stage('git code checkout'){
        try{
            echo 'Checkout the code from Git repository'
            git 'https://github.com/Sanketar/express-node-assingment.git'
        }
        catch(Exception e){
            echo 'Exception occured in Git code checkout stage'
            currentBuild.result = "FAILURE"
            emailext body: '''Dear All,
            The Jenkins job ${JOB_NAME} has been failed. Request you to please have a look at it immediately by clicking on the below link. 
            ${BUILD_URL}''', subject: 'Job ${JOB_NAME} ${BUILD_NUMBER} is failed', to: 'sanket@gmail.com'
        }
    }
    
    stage('Build the Application'){
        echo "Biulding the node application"
        sh 'npm install'       
    }
    
    stage('Containerize the application'){
        echo 'Creating Docker image'
        sh "sudo ${dockerCMD} build -t sanketar/node-app:${tagName} ."
    }
    
    stage ('Pushing it to the DockerHub') {
        echo 'Pushing the docker image to DockerHub'
        withCredentials([usernamePassword(credentialsId: 'dock-pass', passwordVariable:   'DockHubPass', usernameVariable: 'sanketar')]) {
        sh "sudo ${dockerCMD} login -u sanketar -p ${DockHubPass}"
        sh "sudo ${dockerCMD} push sanketar/node-app:${tagName}"    
      }  
    }
    
    stage ('Initiating the Production Enviroment') {
        echo 'Initiating the terraform'
        sh "terraform init"
    }
    
    stage ('Validating the Production Enviroment') {
        echo 'Validating the terraform'
        sh "terraform validate"
    }
    
    stage ('Planing the Production Enviroment') {
        echo 'Planing the terraform'
        sh "terraform plan"
    }
    
    stage ('Creating the Production Enviroment') {
        echo 'Applying the terraform'
        sh "terraform apply -auto-approve"
    }
}    

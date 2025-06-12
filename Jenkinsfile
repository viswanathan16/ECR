pipeline {
   agent any 
   
   environment {
       AWS_REGION = 'us-east-1'
       ECR_REGISTRY = '058264079741.dkr.ecr.us-east-1.amazonaws.com/gfg-ecr'
       ECR_REPO_NAME = 'gfg-ecr'
       DOCKER_IMAGE = "${ECR_REGISTRY}:${env.BUILD_ID}"
}
   stages {
       stage ('Login to ECR') {
           steps { 
               withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-jenkins-creds']]) {
                  sh '''
                      aws ecr get-login-password --region $AWS_REGION |\ 
                      docker login --username AWS --password-stdin $ECR_REGISTRY
                     '''
  }
 }  
}
     stage ('BUILD') {
           steps {
               sh ' docker build -t $DOCKER_IMAGE .'
 }
}
       stage ('push to ECR') {
           steps {
               sh 'docker push $DOCKER_IMAGE'
 }
}

   post {
           failure {
               emailext(
                    to: 'lunajavis05@gmail.com',
                    subject: "Pipeline Failed: ${env.JOB_NAME}:${env.BUILD_NUMBER}",
                    body: "Please check the Pipeline ASAP ${env.BUILD_URL}"
   ) 
  }
 }  
}
}              

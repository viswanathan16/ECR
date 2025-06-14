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
               withCredentials([usernamePassword(credentialsId: 'aws-creds', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY' )]) {
                  sh '''
                      export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                      export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                      aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REGISTRY
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
       stage ('Deploy to minikube') {
           steps {
               withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
              sh '''
                 export KUBECONFIG=$KUBECONFIG
                 envsubst < deployment.yml > deployment_resolved.yml 
                 kubectl  apply -f  deployment_resolved.yml
                 kubectl apply -f service.yml
                 kubectl get pods
                '''
   }
  }
 }
}
    post {
       failure {
           emailext (
               to: 'lunajavis05@gmail.com',
               subject: "Pipeline failed : ${env.JOB_NAME}",
               body: "Please Check the Pipeline ASAP: ${env.BUILD_URL}"
    )
   }
  }      
 }
              

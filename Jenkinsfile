pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:cred>'
       appRegistry = "487300558488.dkr.ecr.ap-south-1.amazonaws.com/project202052301"
       capstoneRegistry = "https://487300558488.dkr.ecr.ap-south-1.amazonaws.com"
       cluster = "202052301"
        service = "202052301_service"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/AIABHISHEK/website.git'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'cred', region: 'ap-south-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
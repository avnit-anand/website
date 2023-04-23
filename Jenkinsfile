pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-east-1:jenkinsCapstonProject'
       appRegistry = "253802446220.dkr.ecr.us-east-1.amazonaws.com/capstonproject"
       capstoneRegistry = "https://253802446220.dkr.ecr.us-east-1.amazonaws.com"
       cluster = "capstonProject"
        service = "capstonProjectService"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/avnit-anand/website'
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
            withAWS(credentials: 'jenkinsCapstonProject', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}

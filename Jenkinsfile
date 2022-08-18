pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('MuhanadSinan-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('MuhannadSinan-aws-secret-access-key')
        AWS_S3_BUCKET = "artefact-bucket-repo"
        ARTIFACT_NAME = "hello-world.dll"
        AWS_EB_APP_NAME = "dotnet-jenkins"
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "Dotnetjenkins-env"
    }
    stages {
        stage('Restor') {
            steps {
                sh "dotnet restore"
            }
        }
         stage('Build') {
            steps { //1. Compile the code (This should create an artifact)

                sh "dotnet build"
            }
        }
         stage('Test') { //2.Run unit test
            steps {
                sh "dotnet test"
            }
        }
	    
        stage('Quality Scan'){
            steps {
                sh "dotnet tool install --global dotnet-sonarscanner"
		sh 'dotnet sonarscanner begin /k:".NET-Application" /d:sonar.host.url="http://15.185.224.95"  /d:sonar.login="sqp_b9dbf13c7993eb537853d206ca26e3b22c40ba5a"'
		sh "dotnet build"
		sh 'dotnet sonarscanner end /d:sonar.login="sqp_b9dbf13c7993eb537853d206ca26e3b22c40ba5a"'
            }
        }

         stage('package') {//3.Publish the report in Junit format

            steps {
                sh "dotnet publish"
            }
            post{
                success{
                    archiveArtifacts artifacts: '**/bin/Debug/net6.0/**.dll', followSymlinks: false
                }
            }
        }
         stage('Publish artifacts to S3 Bucket') {
            steps {//5. Publish the artifacts

                sh "aws configure set region us-east-1"
                sh "aws s3 cp ./bin/Debug/net6.0/pipelines-dotnet-core.dll s3://$AWS_S3_BUCKET/$ARTIFACT_NAME"
            }
         }
         stage('Deploy') {
            steps { //6.Deploy the artifacts generated in the previous step on one of the AWS elastic beanstalk app

                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
            }
         }
    }    

}

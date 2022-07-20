pipeline {

    agent any

    stages {

        stage('Build'){
            steps {
                

                sh "dotnet restore"
            }
        }

        stage('Test'){
            steps {
                
                sh "dotnet test"
            }

        }

        stage('sonar scan'){
            steps {
              
			  sh 'dotnet build'
			  
            }
        }

        stage('Package'){
            steps {
                
                sh "dotnet publish"
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/*.dll', followSymlinks: false
                }
            }
        }

    }

}
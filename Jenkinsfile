@Library('my-shared-library') _

pipeline{

    agent any
    //agent { label 'Demo' }

    parameters{

        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
        string(name: 'ImageName', description: "name of the docker build", defaultValue: 'javapp')
        string(name: 'ImageTag', description: "tag of the docker build", defaultValue: 'v1')
        string(name: 'DockerHubUser', description: "name of the Application", defaultValue: 'ashishgoel48')
    }

    stages{
         
        stage('Git Checkout'){
                    when { expression {  params.action == 'create' } }
            steps{
            gitCheckout(
                branch: "main",
                url: "https://github.com/Ashish-Goel007/Java_app_3.0.git"
            )
            }
        }
         stage('Unit Test maven'){
         
         when { expression {  params.action == 'create' } }

            steps{
               script{
                   
                   mvnTest()
               }
            }
        }
         stage('Integration Test maven'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   mvnIntegrationTest()
               }
            }
        }
        stage('Static code analysis: Sonarqube'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   def SonarQubecredentialsId = 'sonarqube-api'
                   statiCodeAnalysis(SonarQubecredentialsId)
               }
            }
       }
       stage('Quality Gate Status Check : Sonarqube'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   def SonarQubecredentialsId = 'sonarqube-api'
                   QualityGateStatus(SonarQubecredentialsId)
               }
            }
       }
        stage('Maven Build : maven'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   mvnBuild()
               }
            }
        }
        stage('Docker Image Build'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerBuild("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }
         stage('Docker Image Scan: trivy '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImageScan("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }
        stage('Docker Image Push : DockerHub '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImagePush("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }   
        stage('Docker Image Cleanup : DockerHub '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImageCleanup("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }
        /*stage ('Server'){
            steps {
               rtServer (
                 id: "jfrog-server",
                 url: 'http://192.168.29.133:8082/artifactory',
                 username: 'admin',
                  password: 'Password@123',
                  bypassProxy: true,
                   timeout: 300
                        )
            }
        }
        stage('Upload'){
            steps{
                rtUpload (
                 serverId:"jfrog-server" ,
                  spec: '''{
                   "files": [
                      {
                      "pattern": "*.jar",
                      "target": "java-web-app"
                      }
                            ]
                           }''',
                        )
            }
        }
        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "jfrog-server"
                )
            }
        }*/
        stage ('Pushing Jar to Jfrog : python'){
          when { expression {  params.action == 'create' } }
          steps{
            script{
                jfrogPush()
                }
            }
        }
        stage('Sending Report using shell script') {
    steps {
         sh ''' 		#!/bin/sh
	 			echo "hello world"
				CICD=true
				WORKSPACE=/opt/
				JOB_BASE_NAME=Test_demo
				BUILD_NUMBER=10
				if [ $CICD = true ]
				then
				 echo "CI/CD pipe line check"
				file="${WORKSPACE}/basic_report.html"
				REPORTNAME=${JOB_BASE_NAME}_${BUILD_NUMBER}.Test_demo_10
				echo "CICD Check starting"
				  if [ -f "$file" ]; then
						echo "testReport file found sending to artifactory"
						curl -H X-JFrog-Art-Api:'eyJ2ZXIiOiIyIiwidHlwIjoiSldUIiwiYWxnIjoiUlMyNTYiLCJraWQiOiJyUWIyeTgyR2ZUSUdiVXNLRXVHRkl3RVhYaG9xWDd4UWNDODNZUzgtM1c0In0.eyJzdWIiOiJqZmFjQDAxaG5xenpibXA4azRyMGZqdjNtcGEwcTVhL3VzZXJzL2FkbWluIiwic2NwIjoiYXBwbGllZC1wZXJtaXNzaW9ucy9hZG1pbiIsImF1ZCI6IipAKiIsImlzcyI6ImpmZmVAMDAwIiwiaWF0IjoxNzA4NTgxODI1LCJqdGkiOiI3YmVlZTFiOC0yYTYzLTQ4ZGMtODVmYS04Njg1YTkxZWIxN2MifQ.LKg2t5l8W3Omfx9mNJ3c5C-uCvDoVkZvDYcDBGvTYSIcahLbETwuqRtsKNNZNoTtpUK3Jbxx6uBrXoqnI_SVi4A0K5gOCcbt250u4jdkncvggu4QTGTzH1rGpvm7KK2zJdvit8dFsyA2glLc6DiQCk88QfsukdIW6tP0amoYay1AVCxYBjPz1UhejkzZNTiXmfbYtP-5gdpeh6AN8PuD8o07LT__n_LfxbGyFiMSFhTIZrvFynG6LJTg8fWJ7VeniNZhTs83WBY0bmaE8SnQ8gT_5p3LlQC1nvJlJV32bjVQ8iA4DXzo6O_7VzjanDbajLxJ_cFCrK9DP5lO1o-xGw' -T $file http://192.168.29.133:8082/artifactory/example-repo-local/$REPORTNAME.html
				   else
				   echo "testReport file not found"
				  fi
				fi
         '''
            }
        }
    }
}

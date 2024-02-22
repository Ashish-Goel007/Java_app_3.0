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
         sh ''' #!/bin/sh
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
						curl -H X-JFrog-Art-Api:Token -T $file http://192.168.29.133:8082/artifactory/example-repo-local/$REPORTNAME.html
				   else
				   echo "testReport file not found"
				  fi
				fi
         '''
            }
        }
    }
}

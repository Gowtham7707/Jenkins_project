pipeline{
    agent any 
    tools {
        maven 'maven3'
    }
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/Gowtham7707/Jenkins_project.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
        stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar-api-3') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                   }
                    
                }
            }
            stage('Quality Gate Status'){
                
                steps{
                    
                    script{
                        
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api-3'
                    }
                }
            }

        stage('Upload jar file to nexus'){
                
                steps{
                    
                    script{

                       def readPomVersion = readMavenPom file: 'pom.xml'
                       def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "Jenkins-snapshot" : "Jenkins-release"
                       nexusArtifactUploader artifacts: [
                           [
                               artifactId: 'springboot',
                               classifier: '',
                               file: 'target/Uber.jar',
                               type: 'jar'
                           ]
                       ],
                           credentialsId: 'nexus-auth',
                           groupId: 'com.example',
                           nexusUrl: '3.26.131.112:8081', 
                           nexusVersion: 'nexus3',
                           protocol: 'http', 
                           repository: nexusRepo,                 
                           version: "${readPomVersion.version}"
                    }
                }
            }
            stage('Docker image build'){

                steps{ 

                    script{

                           sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                           sh 'docker image tag $JOB_NAME:v1.$BUILD_ID gowthamc121/$JOB_NAME:v1.$BUILD_ID'
                           sh 'docker image tag $JOB_NAME:v1.$BUILD_ID gowthamc121/$JOB_NAME:latest'

                    }

                }
            }
        }
        
}

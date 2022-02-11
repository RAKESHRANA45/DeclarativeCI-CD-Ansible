// def mvn
// def buildInfo
// def DockerTag() {
// 	def tag = sh script: 'git rev-parse HEAD', returnStdout:true
// 	return tag
// 	}
// pipeline {
//   agent { label 'master' }
//     tools {
//       maven 'Maven'
//       jdk 'JAVA_HOME'
//     }

//   environment {
//     SONAR_HOME = "${tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'}"
//     DOCKER_TAG = DockerTag()	  
//   }  
//   stages {
//     stage('Execute_Maven') {
// 	  steps {
// 		  sh 'mvn clean install'		                      
//       }
//     }	
//     stage('War rename') {
// 	  steps {
// 		  	sh 'mv target/*.war target/helloworld.war'
//         }			                      
//       }
//     stage('SonarQube_Analysis') {
//       steps {
// 	    script {
//           scannerHome = tool 'sonar'
//         }
//         withSonarQubeEnv('sonar') {
//       	  sh """${scannerHome}/bin/sonar-scanner"""
//         }
//       } 
//     }	    
//   }
// }   
pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="754733740943"
        AWS_DEFAULT_REGION="us-east-1" 
        IMAGE_REPO_NAME="Rakesh"
        IMAGE_TAG="Rana"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }
   
    stages {
        
         stage('Logging into AWS ECR') {
            steps {
                script {
                sh "sudo aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                }
                 
            }
        }
        
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/sd031/aws_codebuild_codedeploy_nodeJs_demo.git']]])     
            }
        }
  
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh "sudo docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "sudo docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
         }
        }
      }
    }
}

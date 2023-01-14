pipeline {

    options {
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
    }

    agent any

    tools {
        maven 'maven'
    }
    stages {
        stage('Code Compilation') {
            steps {
                echo 'code compilation is starting'
                sh 'mvn clean compile'
				echo 'code compilation is completed'
            }
        }
        stage('Code Package') {
            steps {
                echo 'code packing is starting'
                sh 'mvn clean package'
				echo 'code packing is completed'
            }
        }
        stage('Code Quality Check') {
            steps {
                echo 'code packing is starting'
                sh 'java --version'
				echo 'code packing is completed'
            }
        }
        stage('Building & Tag Docker Image') {
            steps {
                echo 'Starting Building Docker Image'
                sh 'docker build -t sachin163/advik .'
                sh 'docker build -t advik .'
                echo 'Completed  Building Docker Image'
            }
        }
        stage('Docker Image Scanning') {
                    steps {
                        echo 'Docker Image Scanning Started'
                        sh 'java -version'
                        echo 'Docker Image Scanning Started'
                    }
                }
                stage(' Docker push to Docker Hub') {
                   steps {
                      script {
                         withCredentials([string(credentialsId: 'dockercred', variable: 'dockercred')]){
                         sh 'docker login docker.io -u sachin163 -p ${dockercred}'
                         echo "Push Docker Image to DockerHub : In Progress"
                         sh 'docker push sachin163/advik:latest'
                         echo "Push Docker Image to DockerHub : In Progress"
                         sh 'whoami'
                         }
                      }
                    }
                }
            stage(' Docker Image Push to Amazon ECR') {
                       steps {
                          script {
                               withDockerRegistry([credentialsId:'ecr:ap-south-1:awscred', url:"https://026145495181.dkr.ecr.ap-south-1.amazonaws.com"]){
                                              sh """
                                              echo "List the docker images present in local"
                                              docker images
                                              echo "Tagging the Docker Image: In Progress"
                                              docker tag advik:latest 026145495181.dkr.ecr.ap-south-1.amazonaws.com/advik:latest
                                              echo "Tagging the Docker Image: Completed"
                                              echo "Push Docker Image to ECR : In Progress"
                                              docker push 026145495181.dkr.ecr.ap-south-1.amazonaws.com/advik:latest
                                              echo "Push Docker Image to ECR : Completed"
                                              """
                                              }
                          }
                       }
                    }
                    stage('Upload the docker Image to Nexus') {
                               steps {
                                  script {
                                     withCredentials([usernamePassword(credentialsId: 'nexuscred', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
                                     sh 'docker login http://43.205.203.219:8081/repository/advik/ -u admin -p ${PASSWORD}'
                                     echo "Push Docker Image to Nexus : In Progress"
                                     sh 'docker tag advik 43.205.203.219:8081/advik:latest'
                                     sh 'docker push 43.205.203.219:8081/advik'
                                     echo "Push Docker Image to Nexus : Completed"
                                     }
                                  }
                                }
                            }
    }
}

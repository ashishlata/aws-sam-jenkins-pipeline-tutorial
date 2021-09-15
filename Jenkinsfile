pipeline {
  agent any

  environment {
        //branch = 'master'
        BUILD_NUMBER = "${env.BUILD_NUMBER}"
        //scmUrl = 'ssh://git@myScmServer.com/repos/myRepo.git'
        //developmentServer = 'dev-myproject.mycompany.com'
        //stagingServer = 'staging-myproject.mycompany.com'
        //productionServer = 'production-myproject.mycompany.com'
    }
 
  stages {

    stage('install sam-cli') {
      steps {
        sh """
        pip3 install virtualenv
        python3 -m virtualenv venv 
        . venv/bin/activate
        pip3 install aws-sam-cli
        cp -rf venv /var/lib/jenkins/
        """
        stash includes: '**/venv/**/*', name: 'venv'
      }
    }
    stage('Build and Deploy') {
        parallel {
            stage('service 1 execution') {
                    stages{
                        stage('Build') {
                                steps {
                                    dir('Services/Service_1'){
                                        unstash 'venv'
                                        sh 'venv/bin/sam build'
                                        stash includes: '**/.aws-sam/**/*', name: 'aws-sam'
                                    }
                                }
                        }
                        stage('beta') {
                            environment {
                                STACK_NAME = 'sam-app-beta-stage-service-1'
                                S3_BUCKET = 'sam-jenkins-demo-us-west-2-ashish'
                            }
                            steps {
                                dir('Services/Service_1'){
                                    withAWS(credentials: 'Ashish-User', region: 'us-west-2') {
                                    unstash 'venv'
                                    unstash 'aws-sam'
                                    sh 'venv/bin/sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
                                    }

                                }
                                
                            }
                        }
                        stage('prod') {
                            environment {
                                STACK_NAME = 'sam-app-prod-stage-service-1'
                                S3_BUCKET = 'sam-jenkins-demo-us-east-1-ashish'
                            }
                            steps {
                                dir('Services/Service_1'){
                                    withAWS(credentials: 'Ashish-User', region: 'us-east-1') {
                                    unstash 'venv'
                                    unstash 'aws-sam'
                                    sh 'venv/bin/sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
                                    }

                                }
                               
                            }
                        }
                    }
                    
            }
            stage('service 2 execution') {
                    stages{
                        stage('Build') {
                                steps {
                                    dir('Services/Service_2'){
                                        unstash 'venv'
                                        sh 'venv/bin/sam build'
                                        stash includes: '**/.aws-sam/**/*', name: 'aws-sam'
                                    }
                                }
                        }
                        stage('beta') {
                            environment {
                                STACK_NAME = 'sam-app-beta-stage-service-2'
                                S3_BUCKET = 'sam-jenkins-demo-us-west-2-ashish'
                            }
                            steps {
                                dir('Services/Service_2'){
                                    withAWS(credentials: 'Ashish-User', region: 'us-west-2') {
                                    unstash 'venv'
                                    unstash 'aws-sam'
                                    sh 'venv/bin/sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
                                    }

                                }
                                
                            }
                        }
                        stage('prod') {
                            environment {
                                STACK_NAME = 'sam-app-prod-stage-service-2'
                                S3_BUCKET = 'sam-jenkins-demo-us-east-1-ashish'
                            }
                            steps {
                                dir('Services/Service_2'){
                                    withAWS(credentials: 'Ashish-User', region: 'us-east-1') {
                                    unstash 'venv'
                                    unstash 'aws-sam'
                                    sh 'venv/bin/sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
                                    }

                                }
                               
                            }
                        }
                    }
                    
            }
        }

    }
  }
  post {
      success {
          mail to: 'hemantlata321@gmail.com', subject: 'Pipeline successful', body: "${env.BUILD_NUMBER}"

      }
      failure {
          mail to: 'hemantlata321@gmail.com', subject: 'Pipeline failed', body: "${env.BUILD_NUMBER}"
        }
    }
}
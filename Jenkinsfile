pipeline {
  agent any
 
  stages {
    stage('Build and Deploy') {
        parallel {
            stage('service 1 execution') {
                    stages{
                        stage('Build') {
                            steps {
                                dir('Services/Services_1'){
                                    sh 'pip install aws-sam-cli'
                                    sh 'sam build'
                                    stash includes: '**/.aws-sam/**/*', name: 'aws-sam'

                                }
                            }
                        }
                        stage('beta') {
                            environment {
                                STACK_NAME = 'sam-app-beta-stage'
                                S3_BUCKET = 'sam-jenkins-demo-us-west-2-ashish'
                            }
                            steps {
                                dir('Services/Services_1'){
                                    withAWS(credentials: 'Ashish-User', region: 'us-west-2') {
                                    unstash 'aws-sam'
                                    sh 'sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
                                    }

                                }
                               
                            }
                        }
                        stage('prod') {
                            environment {
                                STACK_NAME = 'sam-app-prod-stage'
                                S3_BUCKET = 'sam-jenkins-demo-us-east-1-ashish'
                            }
                            steps {
                                dir('Services/Services_1'){
                                    withAWS(credentials: 'Ashish-User', region: 'us-east-1') {
                                    unstash 'aws-sam'
                                    sh 'sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
                                    }
                                }
                                
                            }
                        }
                    }
                    
                }
        }

    }
  }
}
pipeline {
  agent any
 
  stages {

    stage('Install sam-cli') {
      steps {
        sh 'pip install aws-sam-cli'
      }
    }
    stage('Build') {
      steps {
        sh 'sudo su'
        sh 'sam build'
        stash includes: '**/.aws-sam/**/*', name: 'aws-sam'
      }
    }
    stage('beta') {
      environment {
        STACK_NAME = 'sam-app-beta-stage'
        S3_BUCKET = 'sam-jenkins-demo-us-west-2-ashish'
      }
      steps {
        withAWS(credentials: 'Ashish-User', region: 'us-west-2') {
          unstash 'aws-sam'
          sh 'sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
          dir ('hello-world') {
            sh 'sudo npm ci'
            sh 'sudo npm run integ-test'
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
        withAWS(credentials: 'Ashish-User', region: 'us-east-1') {
          unstash 'aws-sam'
          sh 'sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
        }
      }
    }
  }
}
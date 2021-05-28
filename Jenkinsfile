pipeline {
  agent any
 
  stages {
    stage('Install sam-cli') {
      steps {
        sh 'sudo python3 -m venv venv && sudo venv/bin/pip install aws-sam-cli'
        stash includes: '**/venv/**/*', name: 'venv'
      }
    }
    stage('Build') {
      steps {
        unstash 'venv'
        sh 'sudo venv/bin/sam build'
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
          unstash 'venv'
          unstash 'aws-sam'
          sh 'sudo venv/bin/sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
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
          unstash 'venv'
          unstash 'aws-sam'
          sh 'sudo venv/bin/sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
        }
      }
    }
  }
}
pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        sh 'apt update && apt install -y python3 && apt install -y python3-pip'
        sh 'pip install -r requirements.txt'
      }
    }
    stage('test') {
      steps {
        sh 'cfn-policy-validator validate --template-path template.json --region us-east-1 --template-configuration-file template-configuration.json'
      }   
    }
  }
}
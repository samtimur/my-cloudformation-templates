pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        sh 'apk add python3 && python3 -m ensurepip && pip3 install --upgrade pip setuptools'
        sh 'pip3 install -r requirements.txt'
      }
    }
    stage('test') {
      steps {
        sh 'cfn-policy-validator validate --template-path template.json --region us-east-1 --template-configuration-file template-configuration.json'
      }   
    }
  }
}
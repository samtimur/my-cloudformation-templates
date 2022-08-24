pipeline {
  agent { any }
  stages {
    stage('build') {
      steps {
        sh 'python --version'
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
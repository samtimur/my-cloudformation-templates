podTemplate(containers: [
    containerTemplate(name: 'python', image: 'python:latest', command: 'sleep', args: '30d')
    containerTemplate(name: 'aws-cli', image: 'amazon/aws-cli:latest', tty: true)
  ]) {

    node(POD_LABEL) {

        checkout scm

        stage('Validation') {
            container('python') {
                stage('Install toolkit') {
                    sh '''
                    echo "Install cfn-policy-validator and cfn-lint"
                    python3 --version
                    pip3 install cfn-policy-validator
                    pip3 install cfn-lint
                    '''
                }
                stage('cfn-lint') {
                    sh '''
                    echo "Run cfn-lint"
                    cfn-lint template.json
                    '''
                }
                stage('cfn-policy-validator') {
                    sh '''
                    echo "Run cfn-policy-validator"
                    cfn-policy-validator validate --template-path template.json --region us-east-2 --template-configuration-file template-configuration.json
                    '''
                }
            }
        }

        stage('Deploy') {
            container('aws-cli') {
                stage('Install toolkit') {
                    sh '''
                    echo "Install aws-cli"
                    aws --version
                    '''
                }
            }
        }
    }
}
podTemplate(containers: [
    containerTemplate(
        name: 'python', 
        image: 'python:latest', 
        command: 'sleep', 
        args: '30d')
  ]) {

    node(POD_LABEL) {
        stage('Validation') {
            container('python') {
                stage('Install toolkit') {
                    sh '''
                    echo "Install cfn-policy-validator"
                    python3 --version
                    pip3 install cfn-policy-validator
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

    }
}
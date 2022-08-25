podTemplate(containers: [
    containerTemplate(name: 'python', image: 'python:latest', command: 'sleep', args: '30d'),
    containerTemplate(name: 'aws-cli', image: 'amazon/aws-cli:latest', ttyEnabled: true, command: 'cat')
  ]) {

    node(POD_LABEL) {

        checkout scm

        stage('Validation') {
            container('python') {
                stage('Install toolkit') {
                    sh '''
                    echo "Install requirements"
                    python3 --version
                    pip3 install -r requirements.txt
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
                stage('Deploy stack') {
                    sh '''
                    echo "Create change-set"
                    aws cloudformation create-change-set --stack-name my-cloudformation-templates-stack-jenkins --change-set-name my-cloudformation-templates-change-set --template-body file://template.json --parameters file://parameters.json --change-set-type CREATE
                    '''
                }


            }
        }
    }
}
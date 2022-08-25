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
                stage('Create change set') {
                    sh '''
                    set +e
                    echo "Create change-set"
                    RETURN_CODE=$(aws cloudformation describe-stacks --stack-name my-cloudformation-templates-stack-jenkins)
                    [ $RETURN_CODE -eq 0 ] && CHANGE_SET_TYPE=UPDATE || CHANGE_SET_TYPE=CREATE
                    aws cloudformation create-change-set --stack-name my-cloudformation-templates-stack-jenkins --change-set-name my-cloudformation-templates-change-set --template-body file://template.json --parameters file://parameters.json --capabilities CAPABILITY_NAMED_IAM --change-set-type $CHANGE_SET_TYPE
                    '''
                }
                stage('Execute change set') {
                    sh '''
                    echo "Execute change-set"
                    aws cloudformation execute-change-set --stack-name my-cloudformation-templates-stack-jenkins --change-set-name my-cloudformation-templates-change-set
                    '''
                }
            }
        }
    }
}
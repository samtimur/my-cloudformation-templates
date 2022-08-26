podTemplate(containers: [
    containerTemplate(name: 'python', image: 'python:latest', command: 'sleep', args: '30d'),
    containerTemplate(name: 'aws-cli', image: 'amazon/aws-cli:latest', ttyEnabled: true, command: 'cat')
  ],
  serviceAccount: 'jenkins-service-account' ) {

    node(POD_LABEL) {

        checkout scm

        stage('S3 Test') {
            container('aws-cli') {
                stage('Test s3') {
                    sh '''
                    aws s3 ls
                    '''
                }
            }
        }

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
                    echo "Create change-set"
                    CHANGE_SET_TYPE=UPDATE
                    aws cloudformation describe-stacks --stack-name my-cloudformation-templates-stack-jenkins || CHANGE_SET_TYPE=CREATE
                    aws cloudformation create-change-set --stack-name my-cloudformation-templates-stack-jenkins --change-set-name my-cloudformation-templates-change-set --template-body file://template.json --parameters file://parameters.json --capabilities CAPABILITY_NAMED_IAM --change-set-type $CHANGE_SET_TYPE
                    aws cloudformation wait change-set-create-complete --stack-name my-cloudformation-templates-stack-jenkins --change-set-name my-cloudformation-templates-change-set
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
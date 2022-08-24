podTemplate(containers: [
    containerTemplate(
        name: 'python', 
        image: 'python:latest', 
        command: 'sleep', 
        args: '30d')
  ]) {

    node(POD_LABEL) {
        stage('Get a Python Project') {
            container('python') {
                stage('Build a project') {
                    sh '''
                    echo "Install cfn-policy-validator"
                    python3 --version
                    pip3 install cfn-policy-validator
                    '''
                }
            }
        }

    }
}
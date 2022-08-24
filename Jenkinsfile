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
                    echo "Go Build"
                    python3 --version
                    '''
                }
            }
        }

    }
}
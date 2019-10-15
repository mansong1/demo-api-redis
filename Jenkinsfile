node {
  stage('Checkout') {
    checkout scm
  }

  stage('Build') {
    sh 'docker image build -t mansong/demo-api:latest .'
  }

  stage('Tests') {
    sh 'test/unit.sh'
    sh 'test/test.sh'
    sh 'test/functional.sh'
  }

  stage ('Scan') {
    aquaMicroscanner imageName: 'mansong/demo-api:latest', notCompliesCmd: 'exit 1', onDisallowed: 'fail', outputFormat: 'html'
  }

  stage ('Analysis') {

  }

  stage('Push') {
    withCredentials([
       usernamePassword(credentialsId: 'docker-credentials',
                        usernameVariable: 'USERNAME',
                        passwordVariable: 'PASSWORD')]) {
     sh 'docker login -p "${PASSWORD}" -u "${USERNAME}"'
     sh 'docker image push ${USERNAME}/demo-api:latest'
    }
  }

  stage('Deploy') {
    withCredentials([
       file(credentialsId: 'kube-config',
            variable: 'KUBECONFIG')]) {
     sh 'kubectl apply -f deployment.yaml'
    }
  }
}

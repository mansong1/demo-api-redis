node {
  stage('Checkout') {
    checkout scm
  }

  stage('Tests') {

  }

  stage('Build') {
    sh 'docker image build -t mansong/demo-api:latest .'
  }

  stage ('Scan') {
    aquaMicroscanner imageName: 'mansong/demo-api:latest', notCompliesCmd: 'exit 1', onDisallowed: 'fail', outputFormat: 'html'
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

  stage ('Analysis') {
    sh 'docker run -i kubesec/kubesec:c5a4ff5 scan /dev/stdin < deployment.yaml | jq --exit-status '.score > 10' >/dev/null'
  }

  stage('Deploy') {
    withCredentials([
       file(credentialsId: 'kube-config',
            variable: 'KUBECONFIG')]) {
     sh 'kubectl apply -f deployment.yaml'
    }
  }
}

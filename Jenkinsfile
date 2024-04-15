def label = 'shopagent'
podTemplate(label: label, yaml: '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: build
  annotations:
    sidecar.istio.io/inject: "false"
spec:
  containers:
  - name: build
    image: kumararj/eos-jenkins-agent-base:latest
    command:
    - cat
    tty: true
  - name: docker
    image: docker:24.0.0-rc.1-dind
    securityContext:
      privileged: true
'''
) {
    node(label) {
        stage('CleanWorkspace') {
          cleanWs()
        }
        stage('Checkout SCM') {
          git credentialsId: 'git', url: 'https://github.com/Kumar-arj/microservice-demo-utils.git', branch: 'master'
        }

        stage('Helm Chart') {
          container('build') {
            dir('charts') {
              withCredentials([usernamePassword(credentialsId: 'nexuslogin', usernameVariable: 'username', passwordVariable: 'password')]) {
              sh '/usr/local/bin/helm package micro-services-utils'
              sh 'curl -v -u $username:$password --upload-file micro-services-utils-1.0.tgz http://nexus.k4m.in/repository/sock-shop-helm-local/'
              }
            }
          }
        }
    }
}


@Library('jenkinsBuildLibs@helm')

import com.smarsh.pipeline.helm

properties (
  [[ $class: 'BuildDiscarderProperty', strategy: [ $class: 'LogRotator', numToKeepStr: '30' ] ]]
)

name = "${env.JOB_NAME}"
ID = name.tokenize('/')[0]
def podName = "${ID}-${env.BRANCH_NAME}-${env.BUILD_NUMBER}"

podTemplate(label: podName, containers: [
    containerTemplate(name: 'kubectl', image: 'quay.io/smarsh/jnlp-slave', ttyEnabled: true, alwaysPullImage: true, command: 'cat')
  ])
{

  currentBuild.result = "SUCCESS"

  node(podName) {

    // grab the latest code
    stage('Checkout') {
      checkout scm
    }

    try {

      container('kubectl') {

        stage('Helm Lint') {
          // chart name must match directory name, so move files around
          sh '''
            mkdir elasticsearch
            mv Chart.yaml README.md templates values.yaml elasticsearch
            helm lint elasticsearch
          '''
        }

        stage('Helm DryRun') {
          sh "helm install elasticsearch --dry-run --debug"
        }

        stage('Helm Package Chart') {
          sh "Package up Helm Chart"
        }

        stage('Helm Publish Chart') {
          sh "Publish Helm Chart"
        }

      }

    } catch (err) {
      currentBuild.result = "FAILURE"
    }

  }
}

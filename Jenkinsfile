pipeline {
  agent any

  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    preserveStashes(buildCount: 3)
    skipStagesAfterUnstable()
    timeout(time: 1, unit: 'HOURS')
  }

  triggers {
    pollSCM('H */15 * * *')
  }

  environment {
      CI = true
      HOME = "${env.WORKSPACE}"
  }

  stages {
    stage('build docker images') {
      stages {
        stage('jenkins-slave') {
          steps {
            dir('jenkins-slave') {
              sh 'chmod +x ./build.sh'
              sh './build.sh'
            }
          }
        }

        stage('gcc-toolchain') {
          steps {
            dir('gcc-toolchain') {
              sh 'chmod +x ./build.sh'
              sh './build.sh'
            }
          }
        }

        stage('java-toolchain') {
          steps {
            dir('java-toolchain') {
              sh 'chmod +x ./build.sh'
              sh './build.sh'
            }
          }
        }

        stage('nodejs-toolchain') {
          steps {
            dir('nodejs-toolchain') {
              sh 'chmod +x ./build.sh'
              sh './build.sh'
            }
          }
        }
      }
    }
  }

  post {
    failure {
      script {
        committerEmail = sh(returnStdout: true, script: 'git --no-pager show -s --format=\'%ae\'').trim()
      }

      mail(
        to: "${committerEmail}",
        subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
        body: "Something is wrong with ${env.BUILD_URL}"
      )
    }
  }
}
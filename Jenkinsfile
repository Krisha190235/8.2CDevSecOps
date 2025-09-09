pipeline {
  agent any
  triggers { pollSCM('H/2 * * * *') }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Krisha190235/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps { sh 'npm install' }
    }

    stage('Snyk Auth (non-interactive)') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          sh '''
            if ! command -v snyk >/dev/null 2>&1; then
              npx --yes snyk auth "$SNYK_TOKEN"
            else
              snyk auth "$SNYK_TOKEN"
            fi
          '''
        }
      }
    }

    stage('Run Tests') {
      steps { sh 'npm test || true' }
      post {
        always {
          emailext(
            to: 'krishapatel1902@gmail.com',
            subject: "Build ${currentBuild.currentResult}: Tests Stage",
            body: "Test stage completed with status: ${currentBuild.currentResult}",
            attachLog: true
          )
        }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps { sh 'npm audit || true' }
      post {
        always {
          emailext(
            to: 'krishapatel1902@gmail.com',
            subject: "Build ${currentBuild.currentResult}: Security Scan",
            body: "Security scan completed with status: ${currentBuild.currentResult}",
            attachLog: true
          )
        }
      }
    }
  }
}

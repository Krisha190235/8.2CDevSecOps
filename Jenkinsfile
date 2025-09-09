pipeline {
  agent any

  // Poll every 2 minutes (example) — swap to whatever your brief requires
  triggers { pollSCM('H/2 * * * *') }

  environment {
    // Add this in Jenkins > Manage Jenkins > Credentials as "Secret text"
    // ID must match exactly: SNYK_TOKEN
    SNYK_TOKEN = credentials('SNYK_TOKEN')
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Krisha190235/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Snyk Auth (non-interactive)') {
      // Only needed because your npm test runs "snyk test"
      steps {
        // npx will use local/node_modules if available; falls back to global
        sh '''
          if ! command -v snyk >/dev/null 2>&1; then
            npx --yes snyk auth "$SNYK_TOKEN"
          else
            snyk auth "$SNYK_TOKEN"
          fi
        '''
      }
    }

    stage('Run Tests') {
      steps {
        // keep going even if tests (snyk) fail — matches your brief
        sh 'npm test || true'
      }
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
      steps {
        sh 'npm audit || true'
      }
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

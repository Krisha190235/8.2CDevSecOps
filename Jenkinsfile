pipeline {
  agent any

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

    stage('Run Tests') {
      steps {
        // Allows pipeline to continue despite test failures
        sh 'npm test || true'
      }
    }

    stage('Generate Coverage Report') {
      steps {
        // Ensure coverage report exists even if tests fail
        sh 'npm run coverage || true'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        // Prints known CVEs; do not fail the build for this task
        sh 'npm audit || true'
      }
    }
  }
}


pipeline {
  agent {
    docker {
      image 'node:18-alpine'
    }
  }

  environment {
    SONAR_TOKEN = credentials('SONAR_TOKEN')
  }

  stages {
    stage('Clone Repo') {
      steps {
        git 'https://github.com/tobinguyenx/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm test'
      }
    }

    stage('Security Scan (npm audit)') {
      steps {
        sh 'npm audit --audit-level=high || true'
      }
    }

    stage('Coverage') {
      steps {
        sh 'npm run coverage || true'
      }
    }

    stage('SonarCloud Analysis') {
      steps {
        sh 'npm install -g sonarqube-scanner'
        sh 'sonarqube-scanner'
      }
    }
  }
}

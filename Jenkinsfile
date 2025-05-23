pipeline {
  agent any

  environment {
    SONAR_TOKEN = credentials('SONAR_TOKEN')
  }

  stages {
    stage('Install Node.js & npm') {
      steps {
        sh '''
                    curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
                    sudo apt-get install -y nodejs
                '''
      }
    }

    stage('Clone Repo') {
      steps {
        git branch: 'main', url: 'https://github.com/tobinguyenx/8.2CDevSecOps.git'
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

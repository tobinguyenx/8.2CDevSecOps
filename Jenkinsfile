pipeline {
  agent any

  environment {
    SONAR_TOKEN = credentials('SONAR_TOKEN')
    NODE_VERSION = 'v18.20.2'
    NODE_DIR = "${WORKSPACE}/node"
  }

  stages {
    stage('Clone Repo') {
      steps {
        git branch: 'main', url: 'https://github.com/tobinguyenx/8.2CDevSecOps.git'
      }
    }

    stage('Install Node.js & npm (manual)') {
      steps {
        sh '''
      uname -m
      mkdir -p $NODE_DIR
      curl -o node.tar.gz https://nodejs.org/dist/${NODE_VERSION}/node-${NODE_VERSION}-linux-x64.tar.gz
      tar -xzf node.tar.gz --strip-components=1 -C $NODE_DIR
      rm node.tar.gz
      $NODE_DIR/bin/node -v
      $NODE_DIR/bin/npm -v
    '''
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '$NODE_DIR/bin/npm install'
      }
    }

    stage('Run Tests') {
      steps {
        sh '$NODE_DIR/bin/npm test'
      }
    }

    stage('Security Scan (npm audit)') {
      steps {
        sh '$NODE_DIR/bin/npm audit --audit-level=high || true'
      }
    }

    stage('Coverage') {
      steps {
        sh '$NODE_DIR/bin/npm run coverage || true'
      }
    }

    stage('SonarCloud Analysis') {
      steps {
        sh '''
          $NODE_DIR/bin/npm install -g sonarqube-scanner
          $NODE_DIR/bin/sonarqube-scanner
        '''
      }
    }
  }
}

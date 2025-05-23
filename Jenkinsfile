pipeline {
  agent any

  environment {
    SONAR_TOKEN = credentials('SONAR_TOKEN')
    PATH = "${env.WORKSPACE}/node/bin:${env.PATH}"
  }

  stages {
    stage('Clone Repo') {
      steps {
        git branch: 'main', url: 'https://github.com/tobinguyenx/8.2CDevSecOps.git'
      }
    }

    stage('Install Node.js & npm') {
      steps {
        sh '''
      mkdir -p $WORKSPACE/node
      curl -o node.tar.xz https://nodejs.org/dist/v18.20.2/node-v18.20.2-linux-arm64.tar.xz
      tar -xf node.tar.xz --strip-components=1 -C $WORKSPACE/node
      rm node.tar.xz
      export PATH=$WORKSPACE/node/bin:$PATH
      node -v
      npm -v
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

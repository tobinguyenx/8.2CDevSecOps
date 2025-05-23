pipeline {
  agent any

  environment {
    SONAR_TOKEN = credentials('SONAR_TOKEN')
    PATH = "${env.WORKSPACE}/node/bin:${env.PATH}"
    SONAR_TOKEN = "7faad3fdc810d3d5df18a3081117c00a5878f928"
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
      curl -o node.tar.gz https://nodejs.org/dist/v18.20.2/node-v18.20.2-linux-arm64.tar.gz
      tar -xzf node.tar.gz --strip-components=1 -C $WORKSPACE/node
      rm node.tar.gz
      node -v
      npm -v
    '''
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
                    export PATH=$WORKSPACE/node/bin:$PATH
                    npm install
                '''
      }
    }

    stage('Run Tests') {
      steps {
        sh '''
                    export PATH=$WORKSPACE/node/bin:$PATH
                    npm test || true
                '''
      }
    }

    stage('Security Scan (npm audit)') {
      steps {
        sh '''
                    export PATH=$WORKSPACE/node/bin:$PATH
                    npm audit --audit-level=high || true
                '''
      }
    }

    stage('Coverage') {
      steps {
        sh '''
                    export PATH=$WORKSPACE/node/bin:$PATH
                    npm run coverage || true
                '''
      }
    }

    stage('SonarCloud Analysis') {
      steps {
        sh '''
      export PATH=$WORKSPACE/node/bin:$PATH
      npm install -g sonarqube-scanner
      npx sonarqube-scanner \
        -Dsonar.organization=tobinguyenx \
        -Dsonar.projectKey=tobinguyenx_8.2CDevSecOps \
        -Dsonar.host.url=https://sonarcloud.io \
        -Dsonar.login=$SONAR_TOKEN
    '''
      }
    }
  }
}

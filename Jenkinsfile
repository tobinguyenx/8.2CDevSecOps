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
          mkdir -p test-logs
          npm test > test-logs/test-output.log || true
        '''
      }
      post {
        success {
          emailext(
            subject: 'Test Stage Success',
            body: 'The test stage completed successfully. See the attached log.',
            to: 'nguyentaitino@gmail.com',
            attachmentsPattern: 'test-logs/test-output.log'
          )
        }
        failure {
          emailext(
            subject: 'Test Stage Failed',
            body: 'The test stage failed. See the attached log for details.',
            to: 'nguyentaitino@gmail.com',
            attachmentsPattern: 'test-logs/test-output.log'
          )
        }
      }
    }

    stage('Security Scan (npm audit)') {
      steps {
        sh '''
          export PATH=$WORKSPACE/node/bin:$PATH
          mkdir -p security-scan-logs
          npm audit --audit-level=high > security-scan-logs/npm-audit.log || true
        '''
      }
      post {
        always {
          archiveArtifacts artifacts: 'security-scan-logs/**/*.log', allowEmptyArchive: true
          emailext(
            subject: "Security Scan Stage - Build #${env.BUILD_NUMBER} - ${currentBuild.currentResult}",
            body: """The security scan stage completed with status: ${currentBuild.currentResult}.
Please find the attached logs for more information.""",
            to: 'nguyentaitino@gmail.com',
            attachmentsPattern: 'security-scan-logs/npm-audit.log'
          )
        }
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

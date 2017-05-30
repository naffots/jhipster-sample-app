pipeline {
  agent none
  options {
    skipDefaultCheckout()
  }

  stages {
    stage('Checkout') {
      agent any
      steps {
        checkout scm
        stash(name: 'ws', includes: '**')
      }
    }
    stage('Build Backend') {
      agent {
        docker {
          image 'maven:3-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }
      }
      steps {
        echo 'Hello World'
        unstash 'ws'
        sh './mvnw -B -DskipTests=true clean compile package'
        stash name: 'war', includes: 'target/**/*.war'
      }
      post {
        success {
          archive 'target/**/*.war'
        }
      }
    }
    stage('test Backend') {
      agent {
        docker {
          image 'maven:3-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }
      }
      steps {
        echo 'Testing backend'
        unstash 'ws'
        unstash 'war'
        sh './mvnw 'war' -B test findbugs:findbugs'
      }
      post {
        success {
          junit '**/surefire-reports/**/*.xml'
          findbugs pattern: 'target/**/findbugsXml.xml', unstableTotalAll: '0'
        }
      }
    }
    stage('More Tests') {
      steps {
        echo 'Mooor tests'
      }
    }
  }
}

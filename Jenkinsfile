pipeline {
  agent any 

  stages {
    stage('Build') {
      steps {
      sh 'ant -f build.xml -v'
      } 
    }
    stage('Test') {
      steps {
      sh 'java -jar ./dist/rectangle.jar 4 5'
      }
    }
  }

  post {
    always {
      archive 'dist/*.jar'
    }
  }
}

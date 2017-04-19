pipeline {
  agent BDP

  stages {
    stage('build') {
      steps {		
        sh 'ant -f build.xml -v'	
      }
    }
  }
}

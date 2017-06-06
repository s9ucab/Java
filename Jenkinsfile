pipeline {
  agent none

  stages {
    stage('Unit Test') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      } 
    }

    stage('Build') {
            agent {
        label 'apache'
      }
      steps {
        sh 'ant -f build.xml -v'
      } 
    }
    
    stage('Deploy') {
      agent {
        label 'apache'
      }
      steps {
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
    }
  
    stage("Running on BDP") {
      agent {
        label 'BDP'
      }
      steps {
        sh "wget http://s9ucab1.mylabserver.com/rectangles/all/rectangle_${env_BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD.NUMBER}.jar 3 4"
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
}

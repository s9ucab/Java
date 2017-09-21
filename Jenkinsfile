pipeline {
  agent none 

  options {
    buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '1'))
  }

  stages {
    stage('Build') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f build.xml -v'
      }
      post {
        success{
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
      }
    }

    stage('Unit Tests') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }

    stage('Deploy') {
      agent {
        label 'apache'
      }
      steps {
        sh "cp dist/rectangle.jar /var/www/html/rectangles/all/"
      }
    }

    stage("Run on Node") {
      agent {
        label 'BDP'
      }
      steps {
        sh "wget http://s9ucab2.mylabserver.com/rectangles/all/rectangle.jar"
        sh "java -jar rectangle.jar 3 4"
      }
    }
    stage("Promote Green") {
      agent {
        label 'apache'
      }
      when {
        branch 'master'
      } 
      steps {
        sh "cp /var/www/html/rectangles/all/rectangle.jar /var/www/html/rectangles/green/rectangle.jar"
      }
    }
    stage("Promote Master") {
      agent {
        label 'apache'
      }
      when {
        branch 'development'
      }
      steps {
        echo "Stashing Any Local Changes"
        sh 'git stash'
        echo "Merging Development to Master"
        sh 'git checkout development'
        sh 'git pull'
        sh 'git checkout master'
        sh 'git merge development'
        echo "Pushing to Origin Master"
        sh 'git push origin master'
      }
    }        
  }
}

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
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
    }

    stage("Run on Node") {
      agent {
        label 'BDP'
      }
      steps {
        sh "wget http://s9ucab2.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
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
        sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
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
        sh 'git checkout master'
        sh 'git checkout development .'
        sh 'git add --all'
        sh 'git commit -am merge_to_master'
        echo "Pushing to Origin Master"
        sh 'git push origin master'
      }
    }        
  }
}

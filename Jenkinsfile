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
      post {
        success {
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
      }
    }
    stage('Deploy') {
      agent {
        label 'apache'
      }
      steps {
        sh "mkdir /var/www/html/rectanges/all/${env.BRANCH_NAME}"
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}"
      }
    }
    stage('Centos') {
      agent {
        label 'BDP'
      }
      steps {
        sh "wget http://s9ucab1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage('Debian') {
      agent {
        docker 'openjdk:8u131-jre'
      }
      steps {
        sh "echo hostname"
      }
    } 
    stage('PromoteGreen') {
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
    stage('PromoteBranchMaster') {
      agent {
        label 'apache'
      }
      when {
        branch 'development'
      }
      steps {
        echo "Stashing Any Local Changes"
        sh 'git stash'
        echo "Checking Out Development Branch"
        sh 'git checkout development'
        echo "Checkout Out Master Branch"
        sh 'git checkout master'
        echo "Merging Development into Master Branch"
        sh 'git merge development'
        echo "Pushing to Origin Master"
        sh 'git push origin master'
      }
    }
  }
}

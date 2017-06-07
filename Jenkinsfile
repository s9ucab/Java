pipeline {
  agent none

  environment {
    MAJOR_VERSION = 1
  }

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
        sh "if ![ -d '/var/www/html/rectangles/all/${env.BRANCH_NAME}' ]; then mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}; fi"
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
    }
    stage('Centos') {
      agent {
        label 'BDP'
      }
      steps {
        sh "wget http://s9ucab1.mylabserver.com/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
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
        sh "cp /var/www/html/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      }
    }
      stage('PromoteDevelopmentMaster') {
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
        echo "Checking Out Master Branch"
        sh 'git checkout master'
        echo "Merging Development into Master Branch"
        sh 'git merge -Xours development'
        echo "Pushing to Origin Master"
        sh 'git push -f origin master'
        echo "Tagging the Release"
        sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}" 
        sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
      }
    }
  }
}

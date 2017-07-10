pipeline {
  agent any
  
  stages {
    stage('Unit Test') {
	steps {
	  sh 'ant -f test.xml -v'
	  junit 'reports/result.xml'
      }
    }
    stage('build') {
	steps {
          sh 'ant -f build.xml -v'
      }
    }
    stage('deploy') {
	steps {
	  sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar	/var/www/html/rectangles/all/"
      }
    }
    stage("Run on Centos") {
	steps {
	  sh "wget http://vaishnoavi5.mylabserver.com:8079/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
	  sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage("Run on Debian") {
	agent {
	  docker 'openjdk:8u121-jre'
        steps {
          sh "wget http://vaishnoavi5.mylabserver.com:8079/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
          sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
  }
  post {
        always {
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
      }
    }
}

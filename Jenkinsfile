pipeline {
  agent any

  environment {
    MAJOR_VERSION = 1
  }
 
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
	  sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}"
      }
    }
    stage("Run on Centos") {
	steps {
	  sh "wget http://vaishnoavi5.mylabserver.com:8079/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
	  sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage("Run on Debian") {
	agent {
	  docker 'openjdk:8u121-jre'
        }
        steps {
          sh "wget http://vaishnoavi5.mylabserver.com:8079/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
          sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage("Promote to green") {
	when {
	  branch 'development'
      }
        steps {
	  sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      }
    }
    stage("Promote dev branch to master") {
        when {
          branch 'development'
      }
        steps {
	  echo "Stashing any localchanges"
	  sh 'git stash'
	  echo "Checking out to dev branch"
	  sh 'git checkout development'
	  echo "Checking otu master"
	  sh 'git pull'
 	  sh 'git checkout master'
	  echo "merge dev to master"
	  sh 'git merge development'
	  echo "push origin to master"
	  sh 'git push origin master'
	  echo 'adding tags'
	  sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
	  sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      }
      post {
        success {
          emailext( 
            subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Dev promoted to master"
            body: """<p>${env.JOB_NAME} [${env.BUILD_NUMBER}] Dev promoted to master":</p>
            <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}],/a.&QUOT;</p>""",
            to: "vaishno.avi@gmail.com"
        )
     }
  }
    }
  }
  post {
        always {
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
      }
    }
  post {
	failure {
     	  emailext(
	    subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] failed"
	    body: """<p>${env.JOB_NAME} [${env.BUILD_NUMBER}] failed":</p>
	    <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}],/a.&QUOT;</p>""",
	    to: "vaishno.avi@gmail.com"
	)
     }
  }
}

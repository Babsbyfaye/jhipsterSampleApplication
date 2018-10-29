devsMail = 'babacar.faye@mns-consulting.com'

pipeline {

  agent  any
  options {
        timeout(time: 120, unit: 'MINUTES')
    }
  tools {
    maven "Maven-3.5.2"
  }

  stages {

	stage('Clean Tests') {
      steps {
        sh 'mvn clean test'
      }
    }

     stage('SonarQube Scan') {
       steps{
         script{
             withSonarQubeEnv('sonar-server') {
             sh "mvn sonar:sonar"
            }
         }
        }
     }


    stage("SonarQube Quality Gate") {
      steps{
          script{
            timeout(time: 10, unit: 'MINUTES') {
               def qg = waitForQualityGate()
               if (qg.status != 'OK') {
                 error "Pipeline aborted due to quality gate failure: ${qg.status}"
               }
            }

          }
        }
    }

  }

  post {

       changed {
          emailext attachLog: true, body: '$DEFAULT_CONTENT', subject: '$DEFAULT_SUBJECT',  to: devsMail
       }
        always{
            sh 'mvn clean'
        }
        failure {
                emailext attachLog: true, body: '$DEFAULT_CONTENT', subject: '$DEFAULT_SUBJECT',  to: devsMail
        }

  }
}

pipeline {
  agent any
  tools {
    maven 'maven'
  }

  stages {
    stage('CompileandRunSonarAnalysis') {
      steps {
        withCredentials([string(credentialsId: 'sonartoken', variable: 'sonartoken')]) {
          bat("mvn -Dmaven.test.failure.ignore verify sonar:sonar -Dsonar.login=$sonartoken -Dsonar.projectKey=easybuggy -Dsonar.host.url=http://localhost:9000/")
          echo 'compile stage complete'
        }
      }
    }
    stage('Build') {
      steps {
        withDockerRegistry([credentialsId: "dockertoken", url: ""]) {
          script {
            app = docker.build("masudrana09/testeb")
          }
        }
      }
    }
    stage('RunContainerScan') {
      steps {
        withCredentials([string(credentialsId: 'snyktoken', variable: 'snyktoken')]) {
          script {
            try {
              bat("C:\\snyk\\snyk-win.exe  container test masudrana09/testeb")
            } catch (err) {
              echo err.getMessage()
            }
          }
        }
      }
    }
    stage('RunSnykSCA') {
      steps {
        withCredentials([string(credentialsId: 'snyktoken', variable: 'snyktoken')]) {
          bat("mvn snyk:test -fn")
        }
      }
    }
    stage('RunDASTUsingZAP') {
      steps {
        bat("C:\\Users\\masud\\Downloads\\DevSecOps Course\\ZAP_2.16.1\\zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout C:\\Users\\masud\\Downloads\\DevSecOps Course\\ZAP_2.16.1\\Output.html")
      }
    }

    stage('checkov') {
      steps {
        bat("checkov -s -f main.tf")
      }
    }

  }
}

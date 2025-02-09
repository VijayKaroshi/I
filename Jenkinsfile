pipeline {
  agent any
  tools {
    maven 'Maven_3_8_7'
  }

  stages {
    stage('CompileandRunSonarAnalysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          bat("mvn -Dmaven.test.failure.ignore verify sonar:sonar -Dsonar.login=$SONAR_TOKEN -Dsonar.projectKey=vunerableapplicationmaven -Dsonar.host.url=http://localhost:9000/")
        }
      }
    }
    stage('Build') {
      steps {
        withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
          script {
            app = docker.build("devsecops2306/testeb")
          }
        }
      }
    }     
    stage('RunContainerScan') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          script {
            try {
              bat("D:\\DevSecopstools\\snyk\\snyk-win.exe  container test devsecops2306/testeb")
            } catch (err) {
              echo err.getMessage()
            }
          }
        }
      }
    }
    stage('RunSnykSCA') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          bat("mvn snyk:test -fn")
        }
      }
    }
    stage('RunDASTUsingZAP') {
      steps {
        bat("D:\\DevSecopstools\\ZAP\\ZAP_2.16.0\\zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout D:\\DevSecopstools\\ZAP\\zapoutput\\Output.html")
      }
    }

    stage('checkov') {
      steps {
        bat("checkov -s -f main.tf")
      }
    }

  }
}

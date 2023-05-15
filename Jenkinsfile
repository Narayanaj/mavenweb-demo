pipeline {
    agent any
    tools{
        maven 'maven-3'
    }

    stages {
        stage('git checkout') {
            steps {
                 git url:'https://github.com/bcreddydevops/mavenweb-demo.git', branch: 'main'
                 }
        }
        stage('maven build') {
            steps{
                sh 'mvn clean package'
            }
        }
	stage("Sonarqube analysis"){
            steps{
                script{
                withSonarQubeEnv(installationName: 'sonarqube-8', credentialsId: 'sonarqube-creds') {
                      sh 'mvn sonar:sonar'
                  }
                   timeout(5) {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                }
            }
        }
        stage('nexus upload') {
            steps{
                sh 'mvn clean deploy'
            }
        }
     }
}

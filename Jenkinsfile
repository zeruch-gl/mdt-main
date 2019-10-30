properties([[$class: 'JiraProjectProperty']])
pipeline{
  agent {
    label('Demo')
  }
  stages{
    stage('Sonar') {
            steps {
                withSonarQubeEnv(installationName: 'sonarqube-external', credentialsId: 'sonarqube-server') {
                    script {
                        sonarHome = tool 'sonarscanner4'
                        sh """
                        ${sonarHome}/bin/sonar-scanner -Dsonar.projectKey=student1-project -Dsonar.sources=www
                        """
                    }
                }
            }
            
        }
  }
}
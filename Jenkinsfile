properties([[$class: 'JiraProjectProperty'], parameters([choice(choices: ['RELEASE', 'DEVELOP'], description: '', name: 'ENV'), choice(choices: ['0.1.0'], description: '', name: 'VER')])])
pipeline{
  agent {
    label('Demo')
  }
  tools {
    nodejs('Node12')
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
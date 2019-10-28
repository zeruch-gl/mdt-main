properties([[$class: 'JiraProjectProperty'], parameters([choice(choices: ['RELEASE', 'DEVELOP'], description: '', name: 'ENV'), choice(choices: ['0.1.0'], description: '', name: 'VER')])])
pipeline{
  agent {
    label('vladriabets')
  }
  tools {
    nodejs('Node12')
  }
  stages{
    // stage('CheckoutSCM') {
    //   steps{
    //     git poll: false, url: 'https://github.com/dbielik/mdt-lab.git'
    //   }
    // }
    stage('Sonar') {
            steps {
                withSonarQubeEnv(installationName: 'sonarqube-external', credentialsId: 'sonarqube-server') {
                    script {
                        sonarHome = tool 'sonarscanner4'
                        sh """
                        ${sonarHome}/bin/sonar-scanner -Dsonar.projectKey=www -Dsonar.sources=www
                        """
                    }
                }
            }
            
        }
        stage('Quality gate') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }

    stage('Build') {            
      parallel {
        stage ('build css') {
          steps{
            script{
              sh """
                cd ${WORKSPACE}/www/css
                cleancss -d style.css > ../min/custom-min.css """ 
            }
          }    
        }
        stage ('build js') {
          steps {
            script{
              sh """
                cd ${WORKSPACE}/www/js
                uglifyjs --timings init.js -o ../min/custom-min.js """
            }
          }    
        }        
      }
    }
    stage ('create Archive'){
      steps {
        script { 
          sh """
            cd ${WORKSPACE}/www
            tar --exclude='./css' --exclude='./js' -c -z -f ../site-archive-${params.ENV}-${params.VER}-${BUILD_NUMBER}.tgz ."""
        }
      }
    }        
    stage('Archive when') {
      when {
        expression {
          params.RELEASE == 'RELEASE' 
        }
      }
      steps {
        archive '*.tgz'
      }
    }
  }
}
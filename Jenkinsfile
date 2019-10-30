properties([[$class: 'JiraProjectProperty']])
pipeline{
  agent {
    label('Demo')
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
                        ${sonarHome}/bin/sonar-scanner -Dsonar.projectKey=student1-project -Dsonar.sources=www
                        """
                    }
                }
            }
            
         }
        // stage('Quality gate') {
        //     steps {
        //         waitForQualityGate abortPipeline: true
        //     }
        // }

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
            VER=`cat ${WORKSPACE}/version.txt`
            tar --exclude='./css' --exclude='./js' -c -z -f ../site-archive-student-1.tgz ."""
          nexusArtifactUploader artifacts: [[artifactId: 'site-archive-student-1', \
                                            classifier: '', file: 'site-archive-student-1.tgz', \
                                            type: 'tgz']], \
                                            credentialsId: 'student1-nexus', \
                                            groupId: 'site-archive', \
                                            nexusUrl: 'master.jenkins-practice.tk:9443', \
                                            nexusVersion: 'nexus3', \
                                            protocol: 'https', \
                                            repository: 'student1-repo', \
                                            version: '${VER}-${BUILD_NUMBER}' 
        }
      }
    }      
  }
}
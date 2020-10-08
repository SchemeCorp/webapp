pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
    
    stage ('Check-Secrets'){
      steps {
        sh 'rm -f trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/schemecorp/webapp.git > trufflehog.log'
        sh 'cat trufflehog'
      }
    }
    
    stage ('Source Composition Analysis'){
      steps {
        sh 'rm -f owasp* || true'
        sh 'wget "https://raw.githubusercontent.com/SchemeCorp/webapp/master/owasp-dependency-check.sh"'
        sh 'chmod +x owasp-dependency-check.sh'
        sh 'bash owasp-dependency-check.sh'
      }
    }
  
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
    stage ('Deploy-To-Tomcat') {
      steps {
        sshagent (['tomcat']) {
          sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@3.16.215.168:/home/ubuntu/prod/apache-tomcat-9.0.38/webapps/webapp.war'
        }
      }
    }
    
    
    
  }
}

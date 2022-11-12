import groovy.json.JsonSlurperClassic

def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}
pipeline {
    agent any
    stages {
        
        stage("Sonar: Análisis SonarQube"){
            steps {
                sh "echo 'Calling sonar Service in another docker container!'"
                // Run Maven on a Unix agent to execute Sonar.
                sh "./mvnw clean verify sonar:sonar"
            }
        }    
    }
    post {
        always {
            sh "echo 'fase always executed post'"
        }
        success {
            sh "echo 'fase success'"
        }
        failure {
            sh "echo 'fase failure'"
        }
    }
}
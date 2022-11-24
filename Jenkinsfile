import groovy.json.JsonSlurperClassic

def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}
pipeline {
    agent any
    environment {
        USUARIO = 'Daniel Morales'
    }
    stages {
        stage("Paso 1: Compiliar"){
            steps {
                script {
                    env.STAGE = 'Paso 1: Compilar'
                    sh "echo 'Compile Code!'"
                    // Run Maven on a Unix agent.
                    sh "./mvnw clean compile -e"
                }
            }
        }
        stage("Paso 2: Testear"){
            steps {
                script {
                    env.STAGE = 'Paso 2: Testear'
                    sh "echo 'Test Code!'"
                    // Run Maven on a Unix agent.
                    sh "./mvnw clean test -e" //Descomentar para exito
                    //sh "ssh user@10.1"      //Descomentar para falla
                }
            }
        }
        stage("Paso 3: Build .jar"){
            steps {
                script {
                    env.STAGE = 'Paso 3: Build .jar'
                    sh "echo 'Build .Jar!'"
                    // Run Maven on a Unix agent.
                    sh "./mvnw clean package -e"
                }
            }
            post {
                //record the test results and archive the jar file.
                success {
                    archiveArtifacts artifacts:'build/*.jar'
                }
            }
        }
        stage('Paso 4: Test Sonar con Name-Discovery') {
            steps {
                script {
                    env.STAGE = 'Paso 4: Test Sonar'
                    withSonarQubeEnv('SonarQube') {
                        sh "echo 'Calling sonar Service in another docker container!'"
                        // Run Maven on a Unix agent to execute Sonar
                        sh './mvnw clean verify sonar:sonar -Dsonar.projectKey=custom-project-key'
                    }
                }
            }
        }
    }
    post {
        always {
            sh "echo 'fase always executed post'"
        }
        success {
			slackSend color: 'good', message: "[${env.USUARIO}] [${JOB_NAME}] [${BUILD_TAG}] Ejecucion Exitosa", teamDomain: 'devopsusach20-lzc3526', tokenCredentialId: 'token-slack'
		}
		failure {
			slackSend color: 'danger', message: "[${env.USUARIO}] [${JOB_NAME}] [${BUILD_TAG}] Ejecucion fallida en stage [${STAGE}]", teamDomain: 'devopsusach20-lzc3526', tokenCredentialId: 'token-slack'
		}
    }
}

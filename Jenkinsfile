import groovy.json.JsonSlurperClassic
def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}
pipeline {
    agent any

    stages {
        stage("Paso 0: Download Code and checkout"){
            steps {
                script{
                    checkout(
                            [$class: 'GitSCM',
                            branches: [[name: "feature/sonar" ]],
                            userRemoteConfigs: [[url: 'https://github.com/danmoralesletelier/ejemplo-maven-ceres.git']]])
                }
            }
        }
        stage("Paso 1: Build && Test"){
            steps {
                script{
                    sh "echo 'Build && Test!'"
                    sh "./mvnw clean package -e"
                }
            }
        }

        stage("Paso 3: Curl Springboot maven sleep"){
            steps {
                script{
                    sh "nohup bash ./mvnw spring-boot:run  & >/dev/null"
                    sh "sleep 60 && curl -X GET 'http://localhost:8081/rest/mscovid/test?msg=testing'"
                }
            }
        }
        stage("Paso 3.5: Test con Newman"){
            steps {
                script{
                    sh "newman run /home/ejemplo-maven.postman_collection.json"
                }
            }
        }

        stage("Paso 4: Detener Spring Boot"){
            steps {
                script{
                    sh '''
                        echo 'Process Spring Boot Java: ' $(pidof java | awk '{print $1}')  
                        sleep 20
                        kill -9 $(pidof java | awk '{print $1}')
                    '''
                }
            }
        }
    }
}

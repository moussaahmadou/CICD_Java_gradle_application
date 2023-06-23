pipeline{
    agent any 
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("sonar quality check"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar_token') {
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                    }                 
               } 
        }
    }
     stage("docker build & docker push"){
            steps{
                script{
                             sh '''
                                docker build -t 100.24.24.82:8083/springapp:${VERSION} .
                                docker login -u admin -p admin 100.24.24.82:8083 
                                docker push  100.24.24.82:8083/springapp:${VERSION}
                                docker rmi 100.24.24.82:8083/springapp:${VERSION}   
                            ''' 
                 
                }
            }
         }
        stage("indentifying misconfigs using datree in helm charts"){
            steps{
                script{
                    dir('kubernetes/myapp/') {
                              sh 'helm datree test .'
                      }
                }
            }
        }
     }
}

pipeline{
    agent any 
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("sonar quality check"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                             sh 'chmod +x gradlew'
                             sh './gradlew sonarqube'
                    }                 
               } 
           }
       }
        stage("docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                            sh '''
                                docker build -t 3.87.188.229:8083/springapp:${VERSION} .
                                docker login -u admin -p admin 3.87.188.229:8083 
                                docker push  3.87.188.229:8083/springapp:${VERSION}
                                docker rmi 3.87.188.229:8083/springapp:${VERSION}   
                            ''' 
                    } 
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

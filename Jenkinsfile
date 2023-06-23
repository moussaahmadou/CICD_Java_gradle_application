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
                                docker build -t 18.207.186.16:8083/springapp:${VERSION} .
                                docker login -u admin -p admin 18.207.186.16:8083 
                                docker push  18.207.186.16:8083/springapp:${VERSION}
                                docker rmi 18.207.186.16:8083/springapp:${VERSION}
                            '''
                 
                }
            }
         }
        stage("indentifying misconfigs using datree in helm charts"){
            steps{
                script{
                    dir('kubernetes/myapp/') {
                       # withEnv(['DATREE_TOKEN=c2bf0166-6225-4074-b249-e051487f7da1']){
                              sh 'helm datree test .'
                      }
                    }
                }
            }
        }
     }
}

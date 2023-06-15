pipeline{
    agent any 
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
                                docker build -t 44.205.244.133:8083/springapp:${VERSION} .
                                docker login -u admin -p admin 34.125.214.226:8083 
                                docker push  44.205.244.133:8083/springapp:${VERSION}
                                docker rmi 44.205.244.133:8083/springapp:${VERSION}
                            '''
                 
                }
            }
         }
     }
}

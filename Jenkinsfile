pipeline{
    agent any 
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
                             sh '''
                                docker build -t 3.87.193.96:8083/springapp:${VERSION} .
                                docker login -u admin -p admin 3.87.193.96:8083 
                                docker push  3.87.193.96:8083/springapp:${VERSION}
                                docker rmi 3.87.193.96:8083/springapp:${VERSION}   
                            ''' 
                 
                }
            }
         }
   }
}

pipeline{
    agent any 
    stages{
        stage("sonar quality check"){
            agent {
                docker {
                    //image 'openjdk:11'
                    image 'maven'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar_token') {
                            //sh 'chmod +x gradlew'
                            sh 'mvn clean package sonar:sonar'
                            //sh './gradlew sonarqube'
                    }

                 }  
            }
        }
    } 
}

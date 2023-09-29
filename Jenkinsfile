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
                                docker build -t 44.212.41.185:8083/springapp:${VERSION} .
                                docker login -u admin -p $docker_password 44.212.41.185:8083 
                                docker push 44.212.41.185:8083/springapp:${VERSION}
                                docker rmi 44.212.41.185:8083/springapp:${VERSION}   
                            ''' 
                    } 
                }
            }
         }
        stage("pushing the helm charts to nexus"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                          dir('kubernetes/') {
                             sh '''
                                 helmversion=$( helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ')
                                 tar -czvf  myapp-${helmversion}.tgz myapp/
                                 curl -u admin:$docker_password http://44.212.41.185:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v
                            '''
                          }
                    }
                }
            }
        }
        stage('Deploying application on k8s cluster') {
            steps {
               script{
                   withCredentials([file(credentialsId: 'kubernetes-config', variable: 'KUBECONFIG')]){
                        dir('kubernetes/') {
                            sh 'helm upgrade --install --set image.repository="44.212.41.185:8083/springapp" --set image.tag="${VERSION}" myjavaapp myapp/ ' 
                        }
                    }
               }
            }
        }
   }
}

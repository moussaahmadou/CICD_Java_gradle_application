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
                                docker build -t 44.211.200.87:8083/springapp:${VERSION} .
                                docker login -u admin -p admin 44.211.200.87:8083 
                                docker push  44.211.200.87:8083/springapp:${VERSION}
                                docker rmi 44.211.200.87:8083/springapp:${VERSION}   
                            ''' 
                    } 
                }
            }
         }
        stage("indentifying misconfigs using datree in helm charts"){
            steps{
                script{
                    dir('kubernetes/myapp/') {
                         withEnv(['DATREE_TOKEN=c2bf0166-6225-4074-b249-e051487f7da1']) {
                              sh 'helm datree test .'
                         }
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
                                 curl -u admin:$docker_password http://44.211.200.87:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v
                            '''
                          }
                    }
                }
            }
        }
        stage('Deploying application on k8s cluster') {
            steps {
               script{
                   withCredentials([kubeconfigContent(credentialsId: 'kubernetes-config1', variable: 'KUBECONFIG_CONTENT')]){
                        dir('kubernetes/') {
                          sh 'helm upgrade --install --set image.repository="44.211.200.87:8083/springapp" --set image.tag="${VERSION}" myjavaapp myapp/ ' 
                        }
                    }
               }
            }
        }
   }
}

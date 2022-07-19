def my_name="archit"
def PROD_IP="4493ad94a22c.mylabserver.com"
pipeline {
    agent any
    stages {
        stage ('clonning a git repo') {
            steps {
                echo "$my_name"
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/archit1411/Archit-Mevin-Tripurari.git']]])
            }
        }
        stage ('Building an image'){
            steps{
                echo "I am building an image"
                script {
                    app=docker.build("armalhot/websitetm")
                }
            }
        }
        stage ('Pushing to hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com','docker_hub_login'){
                        app.push("latest")
                    }
                }
            }
        }
        stage ('Logging in to Production Server') {
            steps{
                withCredentials([usernamePassword(credentialsId: 'webserverlogin', usernameVariable: 'USER', passwordVariable: 'PWD')]){
                    sh "sshpass -p $PWD -v ssh -o StrictHostKeyChecking=no $USER@$PROD_IP \"echo LoggedIN\""
                    echo "I have successfully logged in,Lets Deploy"
                    sh "sshpass -p $PWD -v ssh -o StrictHostKeyChecking=no $USER@$PROD_IP \"docker pull armalhot/websitetm\"" 
                }
            }
        }
        stage ('Deploy it into a container') {
            steps{
              withCredentials([usernamePassword(credentialsId: 'webserverlogin', usernameVariable: 'USER', passwordVariable: 'PWD')]){
                sh "sshpass -p $PWD -v ssh -o StrictHostKeyChecking=no $USER@$PROD_IP \"docker container run --name archit -d -P armalhot/websitetm\""

              }  
            }

        }
    }
}

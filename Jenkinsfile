pipeline {
    agent any
    parameters {

        string(name: 'DOCKER_IMAGE', defaultValue:'projeto-calculadora', description:'nome da imagem docker')
        string(name: 'JAR_NAME', defaultValue:'tarefaCalculadora', description:'nome do .jar')

    }
        
    stages{
         
        stage("faz o build do .jar"){
            steps{
                sh 'javac *.java'
                sh 'jar cfe "$JAR_NAME".jar tarefaCalculadora *.class'

            }
        }

    
        stage('cria a imagem docker') {
            steps {
                sh 'docker build -t "$DOCKER_IMAGE":v1.0 .'
            }
        }

     
        stage('envia a imagem para o Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'pedrosj', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                sh 'docker login -u "$USERNAME" -p "$PASSWORD" localhost:8082'
                sh 'docker tag "$DOCKER_IMAGE":v1.0 localhost:8082/"$DOCKER_IMAGE":v1.0'
                sh 'docker push localhost:8082/"$DOCKER_IMAGE":v1.0'

                }
            }
        }

           stage("envia o artefacto para o Nexus") {
            steps{
                withCredentials([usernameColonPassword(credentialsId: 'pedrosj', variable: 'USERPASS')]) {
                sh 'curl -v -u "$USERPASS" --upload-file /var/jenkins_home/workspace/projeto-calculadora/"$JAR_NAME".jar http://localhost:8081/repository/raw-repository/'
            }
        }
    }
    

                stage('limpa o workspace') {
            steps {
                cleanWs()
            }
        }
    } 
}

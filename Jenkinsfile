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
                sh 'jar cfe "$JAR_NAME".jar tarefaCalculadora ./*.class'

            }
        }


        node {
            stage('SCM') {
                git 'https://github.com/foo/bar.git'
            }
    
            stage('SonarQube analysis') {
                withSonarQubeEnv(credentialsId: 'f225455e-ea59-40fa-8af7-08176e86507a', installationName: 'My SonarQube Server') { // You can override the credential to be used
                sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar'
                }
            }
        }    

    
        stage('cria a imagem docker') {
            steps {
                sh 'docker build -t "$DOCKER_IMAGE":v1.0 .'
            }
        }

     
        stage('envia a imagem para o Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'envio-imagensDocker-para-Nexus', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                sh 'docker login -u "$USERNAME" -p "$PASSWORD" localhost:8082'
                sh 'docker tag "$DOCKER_IMAGE":v1.0 localhost:8082/"$DOCKER_IMAGE":v1.0'
                sh 'docker push localhost:8082/"$DOCKER_IMAGE":v1.0'

                }
            }
        }

           stage("envia o artefacto para o Nexus") {
            steps{
                withCredentials([usernameColonPassword(credentialsId: 'envios-repositorioNexus', variable: 'USERPASS')]) {
                sh 'curl -v -u "$USERPASS" --upload "$JAR_NAME".jar http://nexus:8081/repository/raw-repository/'
        
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

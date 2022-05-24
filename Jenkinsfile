pipeline {
    agent any
    tools {
        maven "3.8.5"

    }
    stages {
        stage('Compile and Clean') {
            steps {
                // Run Maven on a Unix agent.

                sh "mvn clean compile"
            }
        }
        stage('deploy') {

            steps {
                sh "mvn package"
            }
        }



        stage('Build Docker image'){

            steps {
                echo "Hello Java docker run"
                sh 'ls'
                sh 'docker run -p 8080:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock --name jenkins jenkins'
//                 sh "docker build -t  davrondev/docker_jenkins_springboot:${BUILD_NUMBER} ."
            }
        }
        stage('Docker Login'){

            steps {
//                  withCredentials([string(credentialsId: 'DockerId', variable: 'Dockerpwd')])

                  sh 'docker login -u davrondev -p 18032002d'

            }
        }
        stage('Docker Push'){
            steps {
                sh "docker push davrondev/docker_jenkins_springboot:${BUILD_NUMBER}"
            }
        }
        stage('Docker deploy'){
            steps {

                sh "docker run -itd -p  8081:8080 davrondev/docker_jenkins_springboot:${BUILD_NUMBER}"
            }
        }
        stage('Archving') {
            steps {
                 archiveArtifacts '**/target/*.jar'
            }
        }
    }
}


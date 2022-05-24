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
                sh "mvn clean install"
            }
        }

       stage('connect docker_jenkins_springboot'){
       steps{
         sh 'docker network create jenkins'
         sh 'docker run --name jenkins-docker --rm --detach \
               --privileged --network jenkins --network-alias docker \
               --env DOCKER_TLS_CERTDIR=/certs \
               --volume jenkins-docker-certs:/certs/client \
               --volume jenkins-data:/var/jenkins_home \
               --publish 2376:2376 \
               docker:dind --storage-driver overlay2'
        }
       }

        stage('Build Docker image'){

            steps {
                echo "Hello Java docker run"
                sh 'ls'
                sh 'docker run --name jenkins-blueocean --restart=on-failure --detach \
                      --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
                      --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
                      --publish 8080:8080 --publish 50000:50000 \
                      --volume jenkins-data:/var/jenkins_home \
                      --volume jenkins-docker-certs:/certs/client:ro \
                      myjenkins-blueocean:2.332.3-1'
//                 sh 'docker run -p 8080:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock --name jenkins jenkins'
                sh "docker build -t  davrondev/docker_jenkins_springboot:${BUILD_NUMBER} ."
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


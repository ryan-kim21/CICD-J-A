pipeline {
    agent {
        docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    stages {
        stage('Clone repository') {
            steps {
                checkout scm
            }
        }
        stage('Build image') {
            steps {
                sh 'docker build -t ryankim5100/testjenkins .'
            }
        }
        stage('Test image') {
            steps {
                sh '''
                    apt-get update
                    apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
                    curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
                    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
                    apt-get update
                    apt-get install -y docker-ce docker-ce-cli containerd.io
                    echo "Tests passed"
                '''
            }
        }
        stage('Push image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DOCKERHUB_CREDENTIALS', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                    sh "docker push ryankim5100/testjenkins:${env.BUILD_NUMBER}"
                }
            }
        }
        stage('Trigger ManifestUpdate') {
            steps {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
            }
        }
    }
}

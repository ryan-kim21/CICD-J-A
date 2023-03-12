pipeline {
    agent any

    stage('Prepare Docker') {
    steps{
        sh 'curl -fsSL https://get.docker.com -o get-docker.sh'
        sh 'sh get-docker.sh'
        sh 'sudo usermod -aG docker jenkins'
    }
}
    stages {
        stage('Clone repository') {
            steps {
                checkout scm
            }
        }
        stage('Build image') {
            environment {
                DOCKER_TAG = "ryankim5100/testjenkins:${env.BUILD_NUMBER}"
            }
            steps {
                script {
                    docker.build(DOCKER_TAG, '.')
                }
            }
        }
        stage('Test image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'DOCKERHUB_CREDENTIALS') {
                        def image = docker.image(DOCKER_TAG)
                        image.inside {
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
                }
            }
        }
        stage('Push image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'DOCKERHUB_CREDENTIALS') {
                        def image = docker.image(DOCKER_TAG)
                        image.push()
                    }
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

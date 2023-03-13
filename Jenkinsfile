pipeline {
    agent any

    stage('Clone repository') {
      
        checkout scm
    }

    stage('Build image') {

       app = docker.build("ryankim5100/testjenkins") //dockerrepo
    }

    stage('Test image') {


    stage('Push image') {

        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {   //jenkins setting for docker hub
            app.push("${env.BUILD_NUMBER}")
        }
    }
    
    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
    }
}